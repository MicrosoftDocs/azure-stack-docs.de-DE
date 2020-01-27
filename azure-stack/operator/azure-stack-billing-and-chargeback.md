---
title: Kundenabrechnung und verbrauchsbasierte Kostenzuteilung in Azure Stack Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie die Abrechnung des Ressourcenverbrauchs für Azure Stack Hub-Benutzer erfolgt, und wie Sie zur Analyse und verbrauchsbasierten Kostenzuteilung auf die Abrechnungsinformationen zugreifen können.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 5bdfb3f39582c198f077e961ff98f5d1352cb942
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75803805"
---
# <a name="usage-and-billing-in-azure-stack-hub"></a>Verbrauch und Abrechnung in Azure Stack Hub

Dieser Artikel erläutert, wie die Abrechnung des Ressourcenverbrauchs für Azure Stack Hub-Benutzer erfolgt, und wie zur Analyse und verbrauchsbasierten Kostenzuteilung auf die Abrechnungsinformationen zugegriffen wird.

Azure Stack Hub erfasst und gruppiert Nutzungsdaten für alle verwendeten Ressourcen und leitet diese Daten dann an Azure Commerce weiter. Azure Commerce stellt Ihnen die Azure Stack Hub-Nutzung genauso in Rechnung wie die Azure-Nutzung.

Sie können auch Nutzungsdaten abrufen und mithilfe eines Abrechnungsadapters in Ihr eigenes System für die Abrechnung und verbrauchsbasierte Kostenzuteilung oder in ein Business Intelligence-Tool wie z. B. Microsoft Power BI exportieren.

## <a name="usage-pipeline"></a>Nutzungspipeline

Jeder Ressourcenanbieter in Azure Stack Hub gibt Nutzungsdaten gemäß Ressourcennutzung aus. Der Nutzungsdienst aggregiert Nutzungsdaten in regelmäßigen Abständen (stündlich und täglich) und speichert sie in der Nutzungsdatenbank. Azure Stack Hub-Operators und -Benutzer können über die Azure Stack Hub-Ressourcennutzungs-APIs auf die gespeicherten Nutzungsdaten zugreifen.

Wenn Sie [Ihre Azure Stack Hub-Instanz bei Azure registriert haben](azure-stack-registration.md), ist Azure Stack Hub zum Senden der Nutzungsdaten an Azure Commerce konfiguriert. Nachdem die Daten in Azure hochgeladen wurden, können Sie über das Abrechnungsportal oder mithilfe der Ressourcennutzungs-APIs von Azure darauf zugreifen. Weitere Informationen dazu, welche Nutzungsdaten an Azure gemeldet werden, finden Sie unter [Nutzungsdatenberichte](azure-stack-usage-reporting.md).  

Die folgende Abbildung zeigt die wichtigsten Komponenten in der Nutzungspipeline:

![Nutzungspipeline](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Welche Nutzungsinformationen kann ich suchen, und wo finde ich sie?

Azure Stack Hub-Ressourcenanbieter wie Compute-, Speicher- und Netzwerkressourcen generieren stündlich Nutzungsdaten für jedes Abonnement. Die Nutzungsdaten enthalten Informationen zur verwendeten Ressource, z.B. den Ressourcennamen, das verwendete Abonnement und die genutzte Menge. Weitere Informationen zu den ID-Ressourcen der Verbrauchseinheiten finden Sie unter [Häufig gestellte Fragen zu Nutzungs-APIs](azure-stack-usage-related-faq.md).

Die gesammelten Nutzungsdaten werden [an Azure gemeldet](azure-stack-usage-reporting.md), um eine Rechnung zu generieren, die im Azure-Abrechnungsportal angezeigt werden kann.

> [!NOTE]  
> Das Melden von Nutzungsdaten ist für das Azure Stack Development Kit (ASDK) und für Benutzer von integrierten Azure Stack Hub-Systemen, die unter dem Kapazitätsmodell lizenziert sind, nicht erforderlich. Weitere Informationen zur Lizenzierung in Azure Stack Hub finden Sie im Datenblatt zu [Paketerstellung und Preisen](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Das Azure-Abrechnungsportal zeigt Nutzungsdaten für kostenpflichtige Ressourcen. Zusätzlich zu den kostenpflichtigen Ressourcen erfasst Azure Stack Hub Nutzungsdaten für eine umfassendere Palette an Ressourcen. Auf diese Daten können Sie in Ihrer Azure Stack Hub-Umgebung über REST-APIs oder PowerShell-Cmdlets zugreifen. Azure Stack Hub-Operators können die Nutzungsdaten für alle Benutzerabonnements abrufen. Einzelne Benutzer können nur ihre eigenen Nutzungsdetails abrufen.

## <a name="usage-reporting-for-multi-tenant-cloud-solution-providers"></a>Nutzungsberichte für mehrinstanzenfähige Cloudlösungsanbieter

Ein mehrinstanzenfähiger Cloud Solution Provider (CSP), der Azure Stack Hub nutzt, sollte einen separaten Nutzungsbericht für jeden Kunden erstellen, damit der Anbieter die Nutzung für verschiedene Azure-Abonnements in Rechnung stellen kann.

Die Identitäten der einzelnen Kunden werden jeweils durch einen anderen Azure Active Directory-Mandanten (Azure AD) dargestellt. Azure Stack Hub unterstützt das Zuweisen eines CSP-Abonnements an jeden Azure AD-Mandanten. Sie können der Azure Stack Hub-Basisregistrierung Mandanten und deren Abonnements hinzufügen. Die Basisregistrierung erfolgt für alle Azure Stack Hub-Instanzen. Wenn ein Abonnement für einen Mandanten nicht registriert ist, kann der Benutzer Azure Stack Hub trotzdem verwenden, und seine Nutzungsdaten werden an das für die Basisregistrierung verwendete Abonnement gesendet.

## <a name="next-steps"></a>Nächste Schritte

- [Registrieren bei Azure Stack Hub](azure-stack-registration.md)
- [Melden von Azure Stack Hub-Nutzungsdaten an Azure](azure-stack-usage-reporting.md)
- [Ressourcennutzungs-API für Anbieter](azure-stack-provider-resource-api.md)
- [Ressourcennutzungs-API für Mandanten](azure-stack-tenant-resource-usage-api.md)
- [Häufig gestellte Fragen zur Nutzung](azure-stack-usage-related-faq.md)
