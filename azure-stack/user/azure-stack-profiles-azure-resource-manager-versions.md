---
title: Von Profilen in Azure Stack Hub unterstützte API-Versionen von Ressourcenanbietern
description: Erfahren Sie mehr über die Azure Resource Manager-API-Versionen, die von Profilen in Azure Stack Hub unterstützt werden.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 3b3ea77dcbb59f2a0471225000aa48624bcb1a15
ms.sourcegitcommit: 390eac7abc94cea1405178e8d6a9358f6488f5d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78231671"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack-hub"></a>Von Profilen in Azure Stack Hub unterstützte API-Versionen von Ressourcenanbietern

In diesem Artikel finden Sie den Ressourcenanbieter und die Versionsnummern für jedes API-Profil, das von Azure Stack Hub verwendet wird. In den Tabellen in diesem Artikel sind die Versionen aufgeführt, die für die einzelnen Ressourcenanbieter und die API-Versionen der Profile unterstützt werden. Jeder Ressourcenanbieter enthält einen Satz mit Ressourcentypen und spezifischen Versionsnummern.

Für das API-Profil gelten drei Benennungskonventionen:

- **Neueste**
- **jjjj-mm-tt-hybrid**
- **jjjj-mm-tt-profile**

Eine Erklärung der API-Profile und des Rhythmus der Versionsveröffentlichung für Azure Stack Hub finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack Hub](azure-stack-version-profiles.md).

> [!NOTE]
> Das API-Profil **latest** enthält die neueste Version der Ressourcenanbieter-API und ist in diesem Artikel nicht aufgeführt.

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Übersicht über das Profil „2019-03-01-hybrid“

| Ressourcenanbieter | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN Gateway: 2017-10-01 |
| Microsoft.Storage (Datenebene) | 2017-11-09 |
| Microsoft.Storage (Steuerungsebene) | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01 (Keine Änderung) |
| Microsoft.Resources (Azure Resource Manager selbst) | 2016-06-01 |
| Microsoft.Authorization (Richtlinienvorgänge) | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

Eine Liste mit den Versionen jedes einzelnen Ressourcentyps für die Anbieter im API-Profil finden Sie unter [Details zum Profil „2019-03-01-hybrid“](#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Details zum Profil „2019-03-01-hybrid“

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Mit der rollenbasierten Zugriffssteuerung können Sie die Aktionen verwalten, die Benutzer in Ihrer Organisation für Ressourcen ausführen können. Sie können Rollen definieren, Benutzern oder Gruppen Rollen zuweisen und Informationen zu Berechtigungen abrufen. Weitere Informationen finden Sie unter [Autorisierung](/rest/api/authorization/).

| Ressourcentypen | API-Versionen |
|---------------------|--------------------|
| Locks | 2016-09-01 |
| Operationen (Operations) | 2015-07-01 |
| Berechtigungen | 2015-07-01 |
| Richtlinienzuweisungen | 2016-12-01 |
| Richtliniendefinitionen | 2016-12-01 |
| Anbietervorgänge | 2015-07-01 |
| Rollenzuweisungen | 2015-07-01 |
| Rollendefinitionen | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Ressourcentyp | API-Version |
|----------------------------------|----------------------|
| Delegierte Anbieterabonnements | 2015-06-01 – Vorschauversion |
| Delegierte Nutzungsaggregate | 2015-06-01 – Vorschauversion |
| Geschätzter Ressourcenaufwand | 2015-06-01 – Vorschauversion |
| Operationen (Operations) | 2015-06-01 – Vorschauversion |
| Nutzungsaggregate von Abonnenten | 2015-06-01 – Vorschauversion |
| Nutzungsaggregate | 2015-06-01 – Vorschauversion |

### <a name="microsoftcompute"></a>Microsoft.Compute

Die Azure Compute-APIs ermöglichen Ihnen den programmgesteuerten Zugriff auf virtuelle Computer und die zugehörigen unterstützenden Ressourcen. Weitere Informationen finden Sie unter [Azure Compute](/rest/api/compute/).

| Ressourcentyp | API-Version |
|---------------------------------------------------------------|-------------|
| Verfügbarkeitsgruppen | 2017-12-01 |
| Standorte | 2017-12-01 |
| Speicherorte/Vorgänge | 2017-12-01 |
| Speicherorte/Herausgeber | 2017-12-01 |
| Speicherorte/Verwendungen | 2017-12-01 |
| Speicherorte/VM-Größen | 2017-12-01 |
| Operationen (Operations) | 2017-12-01 |
| Virtual Machines | 2017-12-01 |
| Virtuelle Computer/Erweiterungen | 2017-12-01 |
| Virtual Machine Scale Sets | 2017-12-01 |
| VM-Skalierungsgruppen/Erweiterungen | 2017-12-01 |
| VM-Skalierungsgruppen/Netzwerkschnittstellen | 2017-12-01 |
| VM-Skalierungsgruppen/virtuelle Computer | 2017-12-01|
| VM-Skalierungsgruppen/virtuelle Computer/Netzwerkschnittstellen | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Ressourcentyp | API-Version |
|------------------|-------------|
| Zusammenstellung | 2015-04-01 |
| Inhalt der Zusammenstellung | 2015-04-01 |
| Auszug aus Zusammenstellung | 2015-04-01 |
| Katalogelemente | 2015-04-01 |
| Operationen (Operations) | 2015-04-01 |
| Portal | 2015-04-01 |
| Suchen, | 2015-04-01 |
| Vorschlagen | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Ressourcentypen | API-Versionen |
|--------------------|--------------------|
| Operationen (Operations) | 2015-04-01 |
| Ereignistypen | 2015-04-01 |
| Ereigniskategorien | 2015-04-01 |
| Metrikdefinitionen | 2018-01-01 |
| Metriken | 2018-01-01 |
| Diagnoseeinstellungen | 2017-05-01-preview |
| Kategorien von Diagnoseeinstellungen | 2017-05-01-preview |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Hiermit verwalten Sie Ihren Schlüsseltresor sowie die Schlüssel, Geheimnisse und Zertifikate im Schlüsseltresor. Weitere Informationen finden Sie in der [Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault/).

| Ressourcentypen | API-Versionen |
|-------------------------|--------------|
| Operationen (Operations) | 2016-10-01 |
| Tresore | 2016-10-01 |
| Tresore/Zugriffsrichtlinien | 2016-10-01 |
| Tresore/Geheimnisse | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Das Ergebnis des Vorgangsaufrufs ist eine Darstellung der Liste verfügbarer Netzwerkcloudvorgänge. Weitere Informationen finden Sie unter [REST-API für Vorgänge](/rest/api/operation/).

| Ressourcentypen | API-Versionen |
|---------------------------|--------------|
| Verbindungen | 2017-10-01 |
| DNS-Zonen | 2016-04-01 |
| Load Balancer | 2017-10-01 |
| Gateway des lokalen Netzwerks | 2017-10-01 |
| Standorte | 2017-10-01|
| Speicherort/Vorgangsergebnisse | 2017-10-01 |
| Speicherorte/Vorgänge | 2017-10-01 |
| Speicherorte/Verwendungen |2017-10-01 |
| Netzwerkschnittstellen | 2017-10-01 |
| Netzwerksicherheitsgruppen | 2017-10-01 |
| Operationen (Operations) | 2017-10-01 |
| Öffentliche IP-Adresse | 2017-10-01 |
| Routingtabellen | 2017-10-01 |
| Gateway des virtuellen Netzwerks | 2017-10-01 |
| Virtuelle Netzwerke | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Mit Azure Resource Manager können Sie die Infrastruktur für Ihre Azure-Lösungen bereitstellen und verwalten. Sie organisieren zugehörige Ressourcen in Ressourcengruppen und stellen Ihre Ressourcen mit JSON-Vorlagen bereit. Eine Einführung in die Bereitstellung und Verwaltung von Ressourcen mit Resource Manager finden Sie in der [Übersicht über Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

| Ressourcentypen | API-Versionen |
|-----------------------------------------|-------------------|
| Bereitstellungen | 2018-05-01 |
| Bereitstellungen/Vorgänge | 2018-05-01 |
| Links | 2018-05-01 |
| Standorte | 2018-05-01 |
| Operationen (Operations) | 2018-05-01 |
| Anbieter | 2018-05-01 |
| ResourceGroups| 2018-05-01 |
| Ressourcen | 2018-05-01/ |
| Abonnements | 2018-05-01 |
| Subscriptions/locations | 2016-06-01 |
| Subscriptions/operationresults | 2018-05-01 |
| Abonnements/Anbieter | 2018-05-01 |
| Subscriptions/ResourceGroups | 2018-05-01 |
| Abonnements/Ressourcengruppen/Ressourcen | 2018-05-01 |
| Abonnements/Ressourcen | 2018-05-01 |
| Abonnements/Tagnamen | 2018-05-01 |
| Abonnements/Tagnamen/Tagwerte | 2018-05-01 |
| Mandanten | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Mit dem Speicherressourcenanbieter (Storage Resource Provider, SRP) können Sie Ihr Speicherkonto und Ihre Schlüssel programmgesteuert verwalten. Weitere Informationen finden Sie in der [Referenz zur Ressourcenanbieter-REST-API für Azure Storage](/rest/api/storagerp/).

| Ressourcentypen | API-Versionen |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| Standorte | 2017-10-01 |
| Speicherorte/Kontingente | 2017-10-01 |
| Operationen (Operations) | 2017-10-01 |
| Speicherkonten | 2017-10-01 |
| Verwendungen | 2017-10-01 |

## <a name="next-steps"></a>Nächste Schritte

- [Installieren von PowerShell für Azure Stack Hub](../operator/azure-stack-powershell-install.md)
- [Konfigurieren der Azure Stack Hub-PowerShell-Umgebung](azure-stack-powershell-configure-user.md)  
