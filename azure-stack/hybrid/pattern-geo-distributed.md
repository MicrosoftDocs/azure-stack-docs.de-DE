---
title: Muster einer geografisch verteilten App für Intelligent Edge unter Verwendung von Azure und Azure Stack Hub.
description: Erfahren Sie mehr über das Muster einer geografisch verteilten App für Intelligent Edge unter Verwendung von Azure und Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 37d4b517eec69ebab72aa3d82733829466b44fda
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875673"
---
# <a name="geo-distributed-pattern"></a>Geografisch verteiltes Muster

Erfahren Sie, wie Sie App-Endpunkte in mehreren Regionen bereitstellen und den Benutzerdatenverkehr je nach Standort- und Complianceanforderungen weiterleiten können.

## <a name="context-and-problem"></a>Kontext und Problem

Organisationen mit umfassenden geografischen Ausmaßen bemühen sich um eine sichere und fehlerfreie Verteilung von und den Zugriff auf diese Daten, während das erforderliche Maß an Sicherheit, Compliance und Leistung pro Benutzer, Standort und Gerät über Grenzen hinweg auch weiterhin sichergestellt werden soll.

## <a name="solution"></a>Lösung

Durch das Routingmuster für geografischen Datenverkehr von Azure Stack Hub und die Verwendung geografisch verteilter Apps kann Datenverkehr basierend auf verschiedenen Metriken an bestimmte Endpunkte weitergeleitet werden. Durch das Erstellen von Traffic Manager mit geografiebasiertem Routing und der Endpunktkonfiguration wird Datenverkehr basierend auf regionalen Anforderungen bzw. unternehmensinternen und internationalen Bestimmungen an Endpunkte weitergeleitet.

![Geografisch verteiltes Muster](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>Komponenten

**Traffic Manager**  
In der Abbildung befindet sich Traffic Manager außerhalb der öffentlichen Cloud, muss allerdings sowohl den Datenverkehr im lokalen Rechenzentrum als auch in der öffentlichen Cloud koordinieren können. Der Balancer leitet Datenverkehr an geografische Standorte weiter.

**Domain Name System (DNS)**  
Das Domain Name System (DNS) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich.

### <a name="public-cloud"></a>Öffentliche Cloud

**Cloudendpunkt**  
Öffentliche IP-Adressen werden zum Weiterleiten des eingehenden Datenverkehrs über Traffic Manager an den Endpunkt der öffentlichen Cloudanwendungsressourcen verwendet.  

### <a name="local-clouds"></a>Lokale Clouds

**Lokaler Endpunkt**  
Öffentliche IP-Adressen werden zum Weiterleiten des eingehenden Datenverkehrs über Traffic Manager an den Endpunkt der öffentlichen Cloudanwendungsressourcen verwendet.

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:

### <a name="scalability"></a>Skalierbarkeit

Das Muster verarbeitet bei erhöhtem Datenverkehr eher das Routing von geografischem Datenverkehr anstelle der Skalierung. Sie können dieses Muster mit anderen Azure-Lösungen und lokalen Lösungen kombinieren. Dieses Muster kann beispielsweise mit dem cloudübergreifenden Skalierungsmuster verwendet werden.

### <a name="availability"></a>Verfügbarkeit

Stellen Sie sicher, dass lokal bereitgestellte Apps in Bezug auf Hochverfügbarkeit konfiguriert sind, die auf der Konfiguration der lokalen Hardware und der Softwarebereitstellung basiert.

### <a name="manageability"></a>Verwaltbarkeit

Mit dem Muster wird sichergestellt, dass zwischen Umgebungen eine nahtlose Verwaltung möglich und eine vertraute Benutzeroberfläche vorhanden ist.

## <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters

- Meine Organisation verfügt über internationale Zweigniederlassungen, für die benutzerdefinierte regionale Sicherheits- und Verteilungsrichtlinien erforderlich sind.
- Alle Niederlassungen meiner Organisation rufen per Pullvorgang Daten zu Mitarbeitern, Geschäft und Einrichtungen ab, sodass Berichterstellungsaktivitäten gemäß den lokalen Bestimmungen und Zeitzonen benötigt werden.
- Anforderungen für eine hohe Skalierbarkeit können durch das horizontale Hochskalieren von Apps erfüllt werden, wobei mehrere App-Bereitstellungen innerhalb einer Region sowie regionsübergreifend erfolgen, um extreme Lastanforderungen zu verarbeiten.
- Die Anwendungen müssen selbst bei einem Ausfall einer einzelnen Region hochverfügbar sein und auf Clientanforderungen reagieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel behandelten Themen:
- In der [Übersicht über Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) erfahren Sie mehr dazu, wie dieser DNS-basierte Lastenausgleich für Datenverkehr funktioniert.
- Unter [Überlegungen zum Entwurf von Hybridanwendungen](overview-app-design-considerations.md) erfahren Sie mehr zu bewährten Methoden und erhalten Antworten auf weitere Fragen.
- Siehe die [Azure Stack-Familie mit Produkten und Lösungen](/azure-stack), um mehr über das gesamte Portfolio von Produkten und Lösungen zu erfahren.

Wenn Sie bereit sind, das Lösungsbeispiel zu testen, fahren Sie mit dem [Bereitstellungsleitfaden für eine Lösung für geografisch verteilte Apps](solution-deployment-guide-geo-distributed.md) fort. In diesem Bereitstellungsleitfaden finden Sie detaillierte Anweisungen zum Bereitstellen und Testen der zugehörigen Komponenten. Sie erfahren, wie Sie Datenverkehr basierend auf unterschiedlichen Metriken an bestimmte Endpunkte weiterleiten, indem Sie das Muster für geografisch verteilte Apps verwenden. Durch die Erstellung eines Traffic Manager-Profils mit Weiterleitung und Endpunktkonfiguration anhand der Geografie wird sichergestellt, dass Informationen basierend auf regionalen Anforderungen, unternehmensinternen und internationalen Bestimmungen und Ihren Datenanforderungen an Endpunkte weitergeleitet werden.