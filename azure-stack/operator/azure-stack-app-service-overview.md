---
title: 'Azure App Service in Azure Stack: Übersicht | Microsoft-Dokumentation'
description: Übersicht über Azure App Service und Azure Functions in Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: bb34fc2322294f9829a8dea02f0812a6503c0197
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75924240"
---
# <a name="azure-app-service-and-azure-functions-on-azure-stack-hub-overview"></a>Übersicht über Azure App Service und Azure Functions in Azure Stack Hub

*Anwendungsbereich: Integrierte Azure Stack Hub-Systeme und Azure Stack Development Kit*

Azure App Service in Azure Stack Hub ist ein PaaS-Angebot (Platform-as-a-Service) von Microsoft Azure, das in Azure Stack Hub verfügbar ist. Mit diesem Dienst können Ihre internen oder externen Kunden Web-, API- und Azure Functions-Apps für beliebige Plattformen oder Geräte erstellen. Sie können Ihre Apps in lokale Apps integrieren und ihre Geschäftsprozesse automatisieren. Azure Stack Hub-Cloudoperatoren können Kunden-Apps auf vollständig verwalteten virtuellen Computern (VMs) mit beliebigen freigegebenen VM-Ressourcen oder dedizierten VMs ausführen.

Mit Azure App Service können Sie Geschäftsprozesse automatisieren und Cloud-APIs hosten. Azure App Service ist ein einzelner integrierter Dienst, mit dem Sie verschiedene Komponenten (wie Websites, REST-APIs und Geschäftsprozesse) in einer zentralen Lösung kombinieren können.

## <a name="why-offer-azure-app-service-on-azure-stack-hub"></a>Gründe für das Angebot von Azure App Service in Azure Stack Hub

Hier sind einige wichtige Features und Funktionen von Azure App Service aufgeführt:

- **Mehrere Sprachen und Frameworks:** Azure App Service bietet erstklassige Unterstützung für ASP.NET, Node.js, Java, PHP und Python. Außerdem können Sie Windows PowerShell und andere Skripts oder ausführbare Dateien auf App Service-VMs ausführen.
- **DevOps-Optimierung**: Richten Sie Continuous Integration und Continuous Deployment mit GitHub, einem lokalen Git oder BitBucket ein. Sie können Updates über Test- und Stagingumgebungen bereitstellen und Ihre Apps in App Service verwalten, indem Sie Azure PowerShell oder die plattformübergreifende Befehlszeilenschnittstelle (CLI) verwenden.
- **Visual Studio-Integration**: Dedizierte Tools in Visual Studio optimieren das Erstellen und Bereitstellen von Apps.

## <a name="app-types-in-app-service"></a>App-Typen in App Service

App Service verfügt über mehrere App-Typen, die jeweils zum Hosten einer bestimmten Workload bestimmt sind:

- [Web-Apps:](/azure/app-service/overview) Hosten von Websites und Web-Apps.
- [API-Apps](/azure/app-service/overview) zum Hosten von REST-APIs.
- Azure Functions zum Hosten ereignisgesteuerter, serverloser Workloads

Der Begriff *App* bezieht sich auf die Hostingressourcen, die für die Ausführung einer Workload dediziert sind. Wenn wir *Web-App* als Beispiel nehmen, verstehen Sie darunter wahrscheinlich sowohl die Computeressourcen als auch die App-Codeelemente, die zusammen für einen Browser die Funktionalität bereitstellen. In Azure App Service handelt es sich bei einer Web-App um die Computeressourcen, die von Azure Stack Hub zum Hosten Ihres App-Codes bereitgestellt werden.

Die App kann aus mehreren App Service-Apps unterschiedlicher Art bestehen. Wenn Ihre App also beispielsweise aus einem Web-Front-End und einem REST-API-Back-End besteht, haben Sie folgende Möglichkeiten:

- Sie können beide Elemente (Front-End und API) für eine einzelne Web-App bereitstellen.
- Sie können Ihren Front-End-Code für eine Web-App und Ihren Back-End-Code für eine API-App bereitstellen.

   [![Übersicht: App Service mit Überwachungsdaten](media/azure-stack-app-service-overview/image01.png "Übersicht: App Service mit Überwachungsdaten")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Was ist ein App Service-Plan?

Der App Service-Ressourcenanbieter verwendet den gleichen Code wie Azure App Service und teilt daher einige gängige Konzepte mit diesem. In App Service wird der Preiscontainer für Apps als *App Service-Plan* bezeichnet. Er steht für die Gruppe von dedizierten VMs, die Ihre Apps enthalten. Ein bestimmtes Abonnement kann mehrere App Service-Pläne enthalten.

In Azure werden freigegebene und dedizierte Worker verwendet. Ein freigegebener Worker unterstützt das mehrinstanzenfähige App-Hosting mit hoher Dichte, und es gibt nur eine Gruppe von freigegebenen Workern. Dedizierte Server werden nur von einem Mandanten verwendet und können drei Größen haben: Klein, Mittel und Groß. Die Anforderungen von lokalen Kunden können mit diesen Begriffen nicht immer beschrieben werden. In App Service in Azure Stack Hub können Ressourcenanbieter-Administratoren die Workerebenen definieren, die sie verfügbar machen möchten. Sie können mehrere Gruppen mit freigegebenen Workern oder verschiedene Gruppen mit dedizierten Workern definieren. Dies richtet sich nach den jeweiligen speziellen Hostinganforderungen. Mit diesen Definitionen der Workerebenen können dann eigene Preis-SKUs definiert werden.

## <a name="portal-features"></a>Features im Portal


Für Azure App Service in Azure Stack Hub wird die gleiche Benutzeroberfläche wie für Azure App Service verwendet. Dies gilt auch für das Back-End. Einige Features sind in Azure Stack Hub jedoch deaktiviert. Die Azure-spezifischen Erwartungen bzw. Dienste, die für diese Features erforderlich sind, sind in Azure Stack Hub derzeit nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- [Voraussetzungen für das Bereitstellen von App Service unter Azure Stack Hub](azure-stack-app-service-before-you-get-started.md)
- [Bereitstellen von App Service in Azure Stack Hub](azure-stack-app-service-deploy.md)

Sie können auch andere [PaaS-Dienste (Platform-as-a-Service)](service-plan-offer-subscription-overview.md) ausprobieren, z. B. den [SQL Server-Ressourcenanbieter](azure-stack-sql-resource-provider-deploy.md) und den [MySQL-Ressourcenanbieter](azure-stack-mysql-resource-provider-deploy.md).
