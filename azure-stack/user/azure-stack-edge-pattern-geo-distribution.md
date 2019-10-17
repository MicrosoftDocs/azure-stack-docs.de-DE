---
title: DevOps-Muster für Intelligent Edge mit Azure Stack | Microsoft-Dokumentation
description: Informationen zum DevOps-Muster für Intelligent Edge mit Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: deab520045d50acefb03691b9b127f99676061a0
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277719"
---
# <a name="geo-distributed-pattern"></a>Geografisch verteiltes Muster

Stellen Sie App-Endpunkte in mehreren Regionen bereit. Sie können den Datenverkehr von Benutzern je nach Speicherort und Complianceanforderungen weiterleiten.

## <a name="context-and-problem"></a>Kontext und Problem

Organisationen mit umfassenden geografischen Ausmaßen bemühen sich um eine sichere und fehlerfreie Verteilung von und den Zugriff auf diese Daten, während das erforderliche Maß an Sicherheit, Compliance und Leistung pro Benutzer, Standort und Gerät über Grenzen hinweg auch weiterhin sichergestellt werden soll.

## <a name="solution"></a>Lösung

Durch das Routingmuster für geografischen Datenverkehr von Azure Stack und die Verwendung geografisch verteilter Apps kann Datenverkehr basierend auf verschiedenen Metriken an bestimmte Endpunkt weitergeleitet werden. Durch das Erstellen von Traffic Manager mit geografiebasiertem Routing und der Endpunktkonfiguration wird Datenverkehr basierend auf regionalen Anforderungen bzw. unternehmensinternen und internationalen Bestimmungen an Endpunkte weitergeleitet.

![Geografisch verteiltes Muster](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**Traffic Manager**  
Auf der Abbildung befindet sich Traffic Manager außerhalb der öffentlichen Cloud, muss allerdings sowohl den Datenverkehr im lokalen Rechenzentrum als auch in der öffentlichen Cloud koordinieren können. Der Balancer leitet Datenverkehr an geografische Standorte weiter.

**Domain Name System (DNS)**  
Das Domain Name System (DNS) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich.

### <a name="public-cloud"></a>Öffentliche Cloud

**Cloudendpunkt**  
Öffentliche IP-Adressen werden zum Weiterleiten des eingehenden Datenverkehrs über Traffic Manager an den Endpunkt der öffentlichen Cloudanwendungsressourcen verwendet.  

### <a name="local-clouds"></a>Lokale Clouds

**Lokaler Endpunkt**  
Öffentliche IP-Adressen werden zum Weiterleiten des eingehenden Datenverkehrs über Traffic Manager an den Endpunkt der öffentlichen Cloudanwendungsressourcen verwendet.

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

Bei der Entscheidung, wie dieses Muster implementiert werden soll, sind die folgenden Punkte zu beachten:

### <a name="scalability-considerations"></a>Überlegungen zur Skalierbarkeit

Das Muster verarbeitet bei erhöhtem Datenverkehr eher das Routing von geografischem Datenverkehr anstelle der Skalierung. Sie können dieses Muster mit anderen Azure-Lösungen und lokalen Lösungen kombinieren. Dieses Muster kann beispielsweise mit dem cloudübergreifenden Skalierungsmuster verwendet werden.

### <a name="availability-considerations"></a>Überlegungen zur Verfügbarkeit

Stellen Sie sicher, dass lokal bereitgestellte Apps in Bezug auf Hochverfügbarkeit konfiguriert sind, die auf der Konfiguration der lokalen Hardware und der Softwarebereitstellung basiert.

### <a name="manageability-considerations"></a>Überlegungen zur Verwaltbarkeit

Mit dem Muster wird sichergestellt, dass zwischen Umgebungen eine nahtlose Verwaltung möglich und eine vertraute Benutzeroberfläche vorhanden ist.

## <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters

-   Meine Organisation verfügt über internationale Zweigniederlassungen, für die benutzerdefinierte regionale Sicherheits- und Verteilungsrichtlinien erforderlich sind.

-   Alle Niederlassungen meiner Organisation rufen per Pullvorgang Daten zu Mitarbeitern, Geschäft und Einrichtungen ab, sodass Berichterstellungsaktivitäten gemäß den lokalen Bestimmungen und Zeitzonen benötigt werden.

-   Anforderungen für eine hohe Skalierbarkeit können durch das horizontale Hochskalieren von Apps erfüllt werden, wobei mehrere App-Bereitstellungen innerhalb einer Region sowie regionsübergreifend erfolgen, um extreme Lastanforderungen zu verarbeiten.

-   Die Anwendungen müssen selbst bei einem Ausfall einer einzelnen Region hochverfügbar sein und auf Clientanforderungen reagieren.

## <a name="example"></a>Beispiel

Es wird beschrieben, wie Sie Datenverkehr basierend auf unterschiedlichen Metriken an bestimmte Endpunkte weiterleiten, indem Sie das Muster für geografisch verteilte Apps verwenden. Durch die Erstellung eines Traffic Manager-Profils mit Weiterleitung und Endpunktkonfiguration anhand der Geografie wird sichergestellt, dass Informationen basierend auf regionalen Anforderungen, unternehmensinternen und internationalen Bestimmungen und Ihren Datenanforderungen an Endpunkte weitergeleitet werden.

[Erstellen einer geografisch verteilten App-Lösung mit Azure und Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Hybrid Cloud-Entwurfsmuster für Azure Stack](azure-stack-edge-pattern-overview.md).
