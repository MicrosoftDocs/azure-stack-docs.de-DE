---
title: Muster für das Entwickeln einer Anwendung in Azure und Azure Stack Hub, die sich cloudübergreifend skalieren lässt.
description: Erfahren Sie, wie Sie mit Azure und Azure Stack Hub eine skalierbare, cloudübergreifende Anwendung erstellen.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: ddc8218aadbf3940ea18ce80012441c5e43c7472
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640239"
---
# <a name="cross-cloud-scaling-pattern"></a>Cloudübergreifendes Skalierungsmuster

Fügen Sie automatisch Ressourcen zu einer vorhandenen Anwendung hinzu, um eine höhere Auslastung zu ermöglichen.

## <a name="context-and-problem"></a>Kontext und Problem

Ihre App kann die Kapazität zum Erfüllen einer unerwartet erhöhten Nachfrage nicht erhöhen. Dieser Mangel an Skalierbarkeit führt dazu, dass Benutzer die App während Spitzennutzungszeiten nicht verwenden können. Die App kann nur von einer festgelegten Anzahl von Benutzern genutzt werden.

Globale Unternehmen benötigen sichere, zuverlässige und verfügbare cloudbasierte Anwendungen. Daher ist es wichtig, erhöhte Anforderungen zu erfüllen und die richtige Infrastruktur zur Unterstützung dieser Anforderungen zu verwenden. Unternehmen haben Probleme beim Ausgleichen von Kosten und bei der Verwaltung der Sicherheit, des Speicherns und der Echtzeitverfügbarkeit von Geschäftsdaten.

Sie können Ihre Anwendung möglicherweise nicht in der öffentlichen Cloud ausführen. Es ist für das Unternehmen aber unter Umständen nicht wirtschaftlich, die für die lokale Umgebung erforderliche Kapazität beizubehalten, um für die App Auslastungsspitzen verarbeiten zu können. Mit diesem Muster können Sie die Elastizität der öffentlichen Cloud für Ihre lokale Lösung verwenden.

## <a name="solution"></a>Lösung

Das cloudübergreifende Skalierungsmuster erweitert eine App, die sich in einer lokalen Cloud befindet, um öffentliche Cloudressourcen. Das Muster wird durch eine Erhöhung oder Abnahme der Nachfrage ausgelöst. Dann werden dementsprechend Ressourcen in der Cloud hinzugefügt oder entfernt. Diese Ressourcen bieten Redundanz, schnelle Verfügbarkeit und geokonformes Routing.

![Cloudübergreifendes Skalierungsmuster](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> Dieses Muster gilt nur für zustandslose Komponenten Ihrer App.

## <a name="components"></a>Komponenten

Das Muster „Cloudübergreifende Skalierung“ besteht aus den folgenden Komponenten.

**Traffic Manager**  

Im Diagramm befindet sich Traffic Manager außerhalb der öffentlichen Cloudgruppe, muss allerdings sowohl den Datenverkehr im lokalen Rechenzentrum als auch in der öffentlichen Cloud koordinieren können. Der Balancer bietet Hochverfügbarkeit für Anwendungen, indem er Endpunkte überwacht und bei Bedarf die Failoverumverteilung ermöglicht.

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
Die Anwendungsressourcen wie VM-Skalierungsgruppen und Container müssen horizontal herunter- oder hochskaliert werden können.

**Benutzerdefinierter Domänenname**  
Verwenden Sie einen benutzerdefinierten Domänennamen für globale Routinganforderungen.

**Öffentliche IP-Adressen**  
Öffentliche IP-Adressen werden zum Weiterleiten des eingehenden Datenverkehrs über Traffic Manager an den Endpunkt der öffentlichen Cloudanwendungsressourcen verwendet. 

## <a name="issues-and-considerations"></a>Probleme und Überlegungen

Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:

### <a name="scalability"></a>Skalierbarkeit

Das Hauptelement der cloudübergreifenden Skalierung ist die Fähigkeit, Skalierung bedarfsabhängig zu ermöglichen. Die Skalierung muss zwischen öffentlicher und lokaler Cloudinfrastruktur erfolgen und einen einheitlichen, zuverlässigen Dienst abhängig vom Bedarf ermöglichen.

### <a name="availability"></a>Verfügbarkeit

Stellen Sie sicher, dass lokal bereitgestellte Apps in Bezug auf Hochverfügbarkeit konfiguriert sind, die auf der Konfiguration der lokalen Hardware und der Softwarebereitstellung basiert.

### <a name="manageability"></a>Verwaltbarkeit

Mit dem cloudübergreifenden Muster wird sichergestellt, dass zwischen Umgebungen eine nahtlose Verwaltung möglich und eine vertraute Benutzeroberfläche vorhanden ist.

## <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters

Verwenden Sie dieses Muster in folgenden Fällen:

- Sie müssen die Kapazität Ihrer App aufgrund von unerwarteten Anforderungen oder regelmäßigen bedarfsgesteuerten Anforderungen erhöhen.
- Sie möchten nicht in Ressourcen investieren, die nur während Spitzenzeiten verwendet werden können. Nutzungsabhängige Abrechnung.

Verwenden Sie dieses Muster in folgenden Fällen nicht:

- Ihre Lösung erfordert, dass Benutzer eine Verbindung über das Internet herstellen.
- Ihr Unternehmen verfügt über lokale Bestimmungen, die erfordern, dass die Ursprungsverbindung von einem lokalen Aufruf erfolgt.
- Ihr Netzwerk erfährt regelmäßig Engpässe, die die Leistung der Skalierung einschränken.
- Ihre Umgebung ist nicht mit dem Internet verbunden und kann nicht auf die öffentliche Cloud zugreifen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel behandelten Themen:
- In der [Übersicht über Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview), in der Sie mehr dazu erfahren, wie dieser DNS-basierte Lastenausgleich für Datenverkehr funktioniert.
- Unter [Überlegungen zum Entwurf von Hybridanwendungen](overview-app-design-considerations.md) erfahren Sie mehr zu bewährten Methoden und erhalten Antworten auf weitere Fragen.
- Siehe die [Azure Stack-Familie mit Produkten und Lösungen](/azure-stack), um mehr über das gesamte Portfolio von Produkten und Lösungen zu erfahren.

Wenn Sie bereit sind, das Lösungsbeispiel zu testen, fahren Sie mit dem [Bereitstellungsleitfaden für eine cloudübergreifende Skalierungslösung](solution-deployment-guide-cross-cloud-scaling.md) fort. In diesem Bereitstellungsleitfaden finden Sie detaillierte Anweisungen zum Bereitstellen und Testen der zugehörigen Komponenten. Sie erfahren, wie Sie eine cloudübergreifende Lösung erstellen, um einen manuell ausgelösten Prozess zum Umschalten von einer in Azure Stack Hub gehosteten Web-App zu einer in Azure gehosteten Web-App bereitzustellen. Sie erfahren auch, wie Sie die automatische Skalierung über Traffic Manager nutzen können, um ein flexibles und skalierbares Cloudhilfsprogramm unter Last zu gewährleisten.
