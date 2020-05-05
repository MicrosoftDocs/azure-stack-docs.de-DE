---
title: 'Azure App Service in Azure Stack Hub: Abrechnungsübersicht und häufig gestellte Fragen'
description: Hier erhalten Sie Informationen zur Abrechnung für Azure App Service in Azure Stack Hub.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: e5ab25785223d1361553f995d22196cd594a6f0a
ms.sourcegitcommit: b185ab34c4c799892948536dd6d1d1b2fc31174e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150274"
---
# <a name="azure-app-service-on-azure-stack-hub-billing-overview-and-faq"></a>Azure App Service in Azure Stack Hub: Abrechnungsübersicht und häufig gestellte Fragen

In diesem Artikel erfahren Sie, wie Cloudbetreibern Azure App Service in Azure Stack Hub in Rechnung gestellt wird und wie sie die Gebühren für die Nutzung des Diensts ihren Mandanten berechnen können.

## <a name="billing-overview"></a>Abrechnungsübersicht

Azure Stack Hub-Cloudbetreiber wählen die Bereitstellung von Azure App Service in Azure Stack Hub auf ihrem Azure Stack Hub-Stempel, um die Mandantenfunktionen von Azure App Service und Azure Functions für ihre Kunden anbieten zu können. Der Azure App Service-Ressourcenanbieter umfasst mehrere Arten von Rollen, die zwischen Infrastruktur- und Workerebenen aufgeteilt werden können.

Infrastrukturrollen werden nicht in Rechnung gestellt, da sie für den Basisbetrieb des Diensts erforderlich sind. Infrastrukturrollen können je nach Bedarf horizontal hochskaliert werden, um die Anforderungen der Mandanten von Cloudbetreibern zu unterstützen. Die Infrastrukturrollen lauten wie folgt:

- Controllers
- Verwaltungsrollen
- Herausgeber
- Front-Ends

Für Workerebenen gibt es zwei Haupttypen: „Freigegeben“ und „Dediziert“. Das Standardanbieterabonnement des Cloudbetreibers wird gemäß den folgenden Kriterien mit der Workernutzung belastet.

## <a name="shared-workers"></a>Freigegebene Worker

Freigegebene Worker sind mehrinstanzenfähig und hostfrei, und App Service-Pläne und verbrauchsbasierte Funktionen (Azure Functions) werden für viele Mandanten gemeinsam genutzt. Für freigegebene Worker werden Verbrauchseinheiten zur Nutzung bereitgestellt, wenn sie im Azure App Service-Ressourcenanbieter als „Bereit“ gekennzeichnet sind.

## <a name="dedicated-workers"></a>Dedizierte Worker

Dedizierte Worker sind an die App Service-Pläne gebunden, die von Mandanten erstellt werden. Bei der SKU „S1“ können Mandanten beispielsweise standardmäßig auf 10 Instanzen skalieren. Wenn ein Mandant einen S1-App Service-Plan erstellt, wird von Azure App Service eine der Instanzen der Workertarif-Skalierungsgruppe „Klein“ dem App Service-Plan dieses Mandanten zugeordnet. Anschließend steht der zugewiesene Worker nicht mehr für die Zuweisung zu anderen Mandanten zur Verfügung. Wenn sich der Mandant für die Skalierung des App Service-Plans auf 10 Instanzen entscheidet, werden neun weitere Worker aus dem verfügbaren Pool entfernt und dem App Service-Plan des Mandanten zugewiesen.

Verbrauchseinheiten werden für dedizierte Worker bereitgestellt, wenn Folgendes gilt:

- Sie sind im Azure App Service-Ressourcenanbieter als „Bereit“ markiert.
- Sie sind einem App Service-Plan zugewiesen.

Bei diesem Abrechnungsmodell können Cloudbetreiber einen Pool mit dedizierten Workern bereitstellen, die von Kunden genutzt werden können, ohne für die Worker zu bezahlen, bevor sie vom App Service-Plan des jeweiligen Mandanten auch wirklich reserviert werden.

Angenommen, Sie haben beispielsweise 20 Worker Workertarif „Klein“. Wenn Sie dann fünf Kunden haben, die jeweils zwei App Service-Pläne vom Typ „S1“ erstellen und den App Service-Plan jeweils zentral auf bis zu zwei Instanzen hochskalieren, haben Sie keine verfügbaren Worker mehr. Aus diesem Grund ist auch keine Kapazität für Ihre Kunden bzw. neuen Kunden vorhanden, die zum Aufskalieren oder Erstellen neuer App Service-Pläne benötigt werden.

Cloudbetreiber können die aktuelle Anzahl von verfügbaren Workern pro Workertarif anzeigen, indem sie sich die Workertarife in der Azure App Service-Konfiguration in der Azure Stack Hub-Verwaltung ansehen.

![Bildschirm „App Service-Workertarife“][1]

## <a name="see-customer-usage-by-using-the-azure-stack-hub-usage-service"></a>Anzeigen der Kundennutzung mit dem Azure Stack Hub-Nutzungsdienst

Cloudbetreiber können die [Azure Stack Hub-Ressourcennutzungs-API für Mandanten](azure-stack-tenant-resource-usage-api.md) abfragen, um Nutzungsinformationen für ihre Kunden abzurufen. Alle einzelnen Verbrauchseinheiten, die App Service zum Beschreiben der Mandantennutzung ausgibt, finden Sie in den [Häufig gestellten Fragen (FAQ) zur Nutzung](azure-stack-usage-related-faq.md). Diese Verbrauchseinheiten werden dann zum Berechnen der Nutzung pro Kundenabonnement verwendet, um Gebühren zu berechnen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>Wie lizenziere ich die in den Voraussetzungen beschriebene erforderliche SQL Server- und Dateiserverinfrastruktur?

Die Lizenzierung der erforderlichen SQL Server- und Dateiserverinfrastruktur für den Azure App Service-Ressourcenanbieter wird in diesem Artikel erläutert: [Voraussetzungen für das Bereitstellen von App Service unter Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server).

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>Unter den häufig gestellten Fragen zur Nutzung sind die Mandantenverbrauchseinheiten aufgeführt, aber nicht die zugehörigen Preise. Wo finde ich diese Informationen?

Als Cloudbetreiber können Sie Ihr eigenes Preismodell auf ihre Endkunden anwenden. Der Nutzungsdienst bietet die Verwendungsmessung. Sie können dann die Menge der Verbrauchseinheiten verwenden, um Ihre Kunden auf Basis des von Ihnen bestimmten Preismodells abzurechnen. Aufgrund dieser Möglichkeit zur Festlegung von Preisen können sich Betreiber von anderen Azure Stack Hub-Betreibern abgrenzen.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>Wie kann ich als CSP die SKUs „Free“ und „Shared“ für Kunden zum Testen des Diensts anbieten?

Als Cloudbetreiber fallen beim Anbieten der SKUs „Free“ und „Shared“ für Sie Kosten an, da sie in freigegebenen Workern gehostet werden. Zur Verringerung dieser Kosten können Sie die Ebene für freigegebene Worker auf das erforderliche Minimum herunterskalieren.

Beispielsweise müssen Sie mindestens über eine A1-Instanz verfügen, um die App Service-Plan-SKUs „Free“ und „Shared“ und nutzungsbasierte Funktionen anbieten zu können. Da freigegebene Worker mehrinstanzenfähig sind, können damit mehrere Kunden-Apps gehostet werden, die jeweils über die App Service-Sandbox isoliert und geschützt sind. Indem Sie den freigegebenen Worker auf diese Weise skalieren, können Sie Ihre Vorleistungen auf die Kosten von einer vCPU pro Monat begrenzen.

Sie können dann ein Kontingent für einen Plan auswählen, damit nur die SKUs „Free“ und „Shared“ angeboten werden und die Anzahl von App Service-Plänen vom Typ „Free“ und „Shared“ begrenzt ist, die von Kunden erstellt werden können.

## <a name="sample-scripts-to-assist-with-billing"></a>Beispielskripts als Hilfe bei der Abrechnung

Das Azure App Service-Team hat PowerShell-Beispielskripts als Hilfe beim Abfragen des Azure Stack Hub-Nutzungsdiensts erstellt. Cloudbetreiber können diese Beispielskripts verwenden, um die Abrechnung für ihre Mandanten vorzubereiten. Die Beispielskripts befinden sich im [Repository für Azure Stack Hub-Tools](https://github.com/Azure/AzureStack-tools) in GitHub. Die App Service-Skripts befinden sich im [Ordner „AppService“ unter „Verwendung“](https://aka.ms/aa6zku8).

Folgende Beispielskripts sind verfügbar:

- [Get-AppServiceBillingRecords](https://aka.ms/aa6zku2): In diesem Beispiel werden die Abrechnungsinformationen für Azure App Service unter Azure Stack Hub über die Azure Stack Hub-Nutzungs-API abgerufen.
- [Get-AppServiceSubscriptionUsage](https://aka.ms/aa6zku6): In diesem Beispiel werden die Nutzungsbeträge pro Abonnement für Azure App Service unter Azure Stack Hub berechnet. Mit diesem Skript werden die Nutzungsbeträge basierend auf den Daten der Nutzungs-API und den Preisen des Cloudbetreibers pro Verbrauchseinheit berechnet.
- [Suspend-UserSubscriptions](https://aka.ms/aa6zku7): In diesem Beispiel wird das Abonnement anhand des vom Cloudbetreibers angegebenen Nutzungslimits angehalten oder aktiviert.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Stack Hub-Ressourcennutzungs-API für Mandanten](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
