---
title: 'Azure App Service in Azure Stack: Abrechnungsübersicht und häufig gestellte Fragen | Microsoft-Dokumentation'
description: Enthält Details zur Verbrauchsmessung und Gebührenabrechnung für Azure App Service in Azure Stack.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 7b11f00c98c6433ad035b6190789276ea5aa6fa7
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213048"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Azure App Service in Azure Stack: Abrechnungsübersicht und häufig gestellte Fragen

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Die Anleitung in diesem Dokument veranschaulicht die Vorgehensweise bei der Abrechnung für Cloudbetreiber, die Azure App Service in Azure Stack anbieten, und wie diese selbst Mandanten die Gebühren für die Nutzung des Diensts berechnen können.

## <a name="billing-overview"></a>Abrechnungsübersicht

Azure Stack-Cloudbetreiber wählen die Bereitstellung von Azure App Service in Azure Stack auf ihrem Azure Stack-Stempel, um die Mandantenfunktionen von Azure App Service und Azure Functions für ihre Kunden anbieten zu können.  Der Azure App Service-Ressourcenanbieter umfasst mehrere Arten von Rollen, die zwischen Infrastruktur- und Workerebenen aufgeteilt werden können.

Infrastrukturrollen werden nicht in Rechnung gestellt, da sie für den Basisbetrieb des Diensts erforderlich sind.  Infrastrukturrollen können je nach Bedarf horizontal hochskaliert werden, um die Anforderungen der Mandanten von Cloudbetreibern zu unterstützen.  Die Infrastrukturrollen lauten wie folgt:

- Controllers
- Verwaltungsrollen
- Herausgeber
- Front-Ends

Für Workerebenen gibt es zwei Haupttypen: „Freigegeben“ und „Dediziert“. Die Workernutzung wird dem Cloudbetreiber gemäß den folgenden Kriterien berechnet.

## <a name="shared-workers"></a>Freigegebene Worker

Freigegebene Worker sind mehrinstanzenfähig und hostfrei, und App Service-Pläne und verbrauchsbasierte Funktionen (Azure Functions) werden für viele Mandanten gemeinsam genutzt. Für freigegebene Worker werden Verbrauchseinheiten zur Nutzung bereitgestellt, wenn sie im Azure App Service-Ressourcenanbieter als „Bereit“ gekennzeichnet sind.

## <a name="dedicated-workers"></a>Dedizierte Worker

Dedizierte Worker sind an die App Service-Pläne gebunden, die von Mandanten erstellt werden. Bei der SKU „S1“ können Mandanten beispielsweise standardmäßig auf zehn Instanzen skalieren. Wenn ein Mandant einen S1-App Service-Plan erstellt, wird von Azure App Service eine der Instanzen der Workertarif-Skalierungsgruppe „Klein“ dem App Service-Plan dieses Mandanten zugeordnet. Anschließend steht der zugewiesene Worker nicht mehr für die Zuweisung zu anderen Mandanten zur Verfügung.  Wenn sich der Mandant für die Skalierung des App Service-Plans auf zehn Instanzen entscheidet, werden weitere neun Worker aus dem verfügbaren Pool entfernt und dem App Service-Plan des Mandanten zugewiesen.

Verbrauchseinheiten werden für dedizierte Worker bereitgestellt, wenn Folgendes gilt:

- Sie sind im Azure App Service-Ressourcenanbieter als „Bereit“ markiert.
- Sie sind einem App Service-Plan zugewiesen.

Aus diesem Grund können Cloudbetreiber bei diesem Abrechnungsmodell einen Pool mit dedizierten Workern bereitstellen, die von Kunden genutzt werden können. Für die Worker muss dann erst bezahlt werden, wenn sie vom App Service-Plan des jeweiligen Mandanten auch wirklich reserviert wurden. Wenn Sie beispielsweise über 20 Worker im Workertarif „Klein“ verfügen und dann fünf ihrer Kunden jeweils zwei App Service-Pläne vom Typ „S1“ erstellen und den App Service-Plan jeweils zentral auf bis zu zwei Instanzen hochskalieren, sind keine verfügbaren Worker vorhanden. Aus diesem Grund ist auch keine Kapazität für Ihre Kunden bzw. neuen Kunden vorhanden, die zum horizontalen Hochskalieren oder Erstellen neuer App Service-Pläne benötigt werden. Cloudbetreiber können die aktuelle Anzahl von verfügbaren Workern pro Workertarif anzeigen, indem sie sich die Workertarife in der Azure App Service-Konfiguration in der Azure Stack-Verwaltung ansehen.

![App Service-Workertarife][1]

## <a name="see-customer-usage-using-the-azure-stack-usage-service"></a>Anzeigen der Kundennutzung mit dem Azure Stack-Nutzungsdienst

Cloudbetreiber können die [Azure Stack-Mandantennutzungs-API](azure-stack-tenant-resource-usage-api.md) abfragen, um Informationen für ihre Kunden abzurufen. Alle individuellen Verbrauchseinheiten, die von App Service zur Mandantennutzung bereitgestellt werden, finden Sie unter [Häufig gestellte Fragen zur Azure Stack-Nutzungs-API](azure-stack-usage-related-faq.md). Diese Verbrauchseinheiten können dann zum Berechnen der Nutzung pro Kundenabonnement verwendet werden, um daraus die Gebühren zu berechnen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-pre-requisites"></a>Lizenzieren der in den Voraussetzungen beschriebenen erforderlichen SQL Server- und Dateiserverinfrastruktur

Die Lizenzierung der SQL- und Dateiserverinfrastruktur, die für den Azure App Service-Ressourcenanbieter erforderlich ist, wird im Artikel [Bevor Sie beginnen](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server) zu Azure App Service in Azure Stack behandelt.

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>Unter den häufig gestellten Fragen zur Nutzung sind die Mandantenverbrauchseinheiten aufgeführt, aber nicht die zugehörigen Preise. Wo finde ich diese Informationen?

Cloudbetreiber können ein eigenes Preismodell für ihre Endkunden anwenden. Der Nutzungsdienst stellt die Messung der Verbrauchseinheiten zur Nutzung bereit, und der Cloudbetreiber verwendet dann die gemessene Menge, um die Gebühren für seine Kunden anhand des gewählten Preismodells in Rechnung zu stellen. Aufgrund dieser Möglichkeit zur Festlegung von Preisen können sich Betreiber von anderen Azure Stack-Betreibern abgrenzen.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Stack-Mandantennutzungs-API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png