---
title: Cloudübergreifende Skalierungsmuster für Intelligent Edge mit Azure Stack | Microsoft-Dokumentation
description: Informationen zum cloudübergreifenden Skalierungsmuster für Intelligent Edge mit Azure Stack
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
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2b97be7ecfffe83e560d32fbaac6480c8c17bb88
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856302"
---
# <a name="cross-cloud-scaling-pattern"></a>Cloudübergreifendes Skalierungsmuster

Fügen Sie automatisch Ressourcen zu einer vorhandenen Anwendung hinzu, um eine höhere Auslastung zu ermöglichen.

## <a name="context-and-problem"></a>Kontext und Problem

Ihre App kann die Kapazität zum Erfüllen unerwarteter erhöhter Anforderungen nicht erhöhen. Dies führt dazu, dass Benutzer die App während der Spitzenzeiten nicht verwenden können. Die App kann nur von einer festgelegten Anzahl von Benutzern genutzt werden.

Globale Unternehmen benötigen sichere, zuverlässige und verfügbare cloudbasierte Anwendungen. Daher ist es wichtig, erhöhte Anforderungen zu erfüllen und die richtige Infrastruktur zur Unterstützung dieser Anforderungen zu verwenden. Unternehmen haben Probleme beim Ausgleichen von Kosten und bei der Verwaltung der Sicherheit, des Speicherns und der Echtzeitverfügbarkeit von Geschäftsdaten.

Sie können Ihre Anwendung möglicherweise nicht in der öffentlichen Cloud ausführen. Es ist für das Unternehmen aber unter Umständen nicht wirtschaftlich, die für die lokale Umgebung erforderliche Kapazität beizubehalten, um für die App Auslastungsspitzen verarbeiten zu können. Mit diesem Muster können Sie die Elastizität der öffentlichen Cloud für Ihre lokale Lösung verwenden.

## <a name="solution"></a>Lösung

Das cloudübergreifende Skalierungsmuster erweitert eine App, die sich in einer lokalen Cloud befindet, um öffentliche Cloudressourcen. Das Muster wird durch eine Erhöhung oder Abnahme der Nachfrage ausgelöst. Dann werden dementsprechend Ressourcen in der Cloud hinzugefügt oder entfernt. Diese Ressourcen bieten Redundanz, schnelle Verfügbarkeit und geokonformes Routing.

![Cloudübergreifendes Skalierungsmuster](media/azure-stack-edge-pattern-cross-cloud-scaling/cross-cloud-scaling.png)

> Hinweis:  
> Dieses Muster gilt nur für zustandslose Apps.

Das cloudübergreifende Skalierungsmuster besteht aus den folgenden Komponenten:

**Traffic Manager**  
**Auf der Abbildung befindet sich Traffic Manager außerhalb der öffentlichen Cloudgruppe, muss allerdings sowohl den Datenverkehr im lokalen Rechenzentrum als auch in der öffentlichen Cloud koordinieren können. Der Balancer bietet Hochverfügbarkeit für Anwendungen, indem er Endpunkte überwacht und bei Bedarf die Failoverumverteilung ermöglicht.

**Domain Name System (DNS)**  
Das Domain Name System (DNS) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich.

### <a name="cloud"></a>Cloud

**Gehosteter Buildserver**  
Umgebung zum Hosten der Buildpipeline.

**Anwendungsressourcen**  
Die Anwendungsressourcen (wie virtuelle ScaleSets-Computer und Container) müssen horizontal herunter- oder hochskaliert werden können.

**Benutzerdefinierter Domänenname**  
Verwenden Sie einen benutzerdefinierten Domänennamen für globale Routinganforderungen.

**Öffentliche IP-Adressen**  
Öffentliche IP-Adressen werden zum Weiterleiten des eingehenden Datenverkehrs über Traffic Manager an den Endpunkt der öffentlichen Cloudanwendungsressourcen verwendet.  

### <a name="local-cloud"></a>Lokale Cloud

**Gehosteter Buildserver**  
Umgebung zum Hosten der Buildpipeline.

**Anwendungsressourcen**  
Die Anwendungsressourcen (wie virtuelle ScaleSets-Computer und Container) müssen horizontal herunter- oder hochskaliert werden können.

**Benutzerdefinierter Domänenname**  
Verwenden Sie einen benutzerdefinierten Domänennamen für globale Routinganforderungen.

**Öffentliche IP-Adressen**  
Öffentliche IP-Adressen werden zum Weiterleiten des eingehenden Datenverkehrs über Traffic Manager an den Endpunkt der öffentlichen Cloudanwendungsressourcen verwendet. 

## <a name="issues-and-considerations"></a>Probleme und Überlegungen


Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:

### <a name="scalability-considerations"></a>Überlegungen zur Skalierbarkeit

Die wichtigste Komponente der cloudübergreifenden Skalierung ist die Möglichkeit, eine On-Demand-Skalierung zwischen der öffentlichen und lokalen Cloudinfrastruktur bereitzustellen und für jede Auslastung für einen konsistenten, zuverlässigen Dienst zu sorgen.

### <a name="availability-considerations"></a>Überlegungen zur Verfügbarkeit

Stellen Sie sicher, dass lokal bereitgestellte Apps in Bezug auf Hochverfügbarkeit konfiguriert sind, die auf der Konfiguration der lokalen Hardware und der Softwarebereitstellung basiert.

### <a name="manageability-considerations"></a>Überlegungen zur Verwaltbarkeit

Mit dem cloudübergreifenden Muster wird sichergestellt, dass zwischen Umgebungen eine nahtlose Verwaltung möglich und eine vertraute Benutzeroberfläche vorhanden ist.

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters

Verwenden Sie dieses Muster in folgenden Fällen:

-   Sie müssen die Kapazität Ihrer App aufgrund von unerwarteten Anforderungen oder regelmäßigen bedarfsgesteuerten Anforderungen erhöhen.

-   Sie möchten nicht in Ressourcen investieren, die nur während Spitzenzeiten verwendet werden können. Nutzungsabhängige Abrechnung.

Verwenden Sie dieses Muster in folgenden Fällen nicht:

-   Ihre Lösung erfordert, dass Benutzer eine Verbindung über das Internet herstellen.

-   Ihr Unternehmen verfügt über lokale Bestimmungen, die erfordern, dass die Ursprungsverbindung von einem lokalen Aufruf erfolgt.

-   Ihr Netzwerk erfährt regelmäßig Engpässe, die die Leistung der Skalierung einschränken.

-   Ihre Umgebung ist nicht mit dem Internet verbunden und kann nicht auf die öffentliche Cloud zugreifen.

## <a name="example"></a>Beispiel

Es wird beschrieben, wie Sie eine cloudübergreifende Lösung erstellen, um einen manuell ausgelösten Prozess zum Umschalten von einer unter Azure Stack gehosteten Web-App zu einer unter Azure gehosteten Web-App mit automatischer Skalierung per Traffic Manager bereitzustellen. So ist dafür gesorgt, dass das Cloudhilfsprogramm auch bei hoher Last flexibel und skalierbar bleibt.

[Erstellen von cloudübergreifenden Skalierungslösungen mit Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-cloud-burst)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Hybrid Cloud-Entwurfsmuster für Azure Stack](azure-stack-edge-pattern-overview.md).
