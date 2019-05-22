---
title: Von Profilen in Azure Stack unterstützte API-Versionen von Ressourcenanbietern | Microsoft-Dokumentation
description: Hier erfahren Sie mehr zur Azure Resource Manager-Version, die von den Profilen in Azure Stack unterstützt werden.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 2e73e6e00503171aafea4ae254af0427a3e245ac
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212323"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Von Profilen in Azure Stack unterstützte API-Versionen von Ressourcenanbietern

In diesem Artikel finden Sie den Ressourcenanbieter und die Versionsnummern für jedes API-Profil, das von Azure Stack verwendet wird. In den Tabellen in diesem Artikel sind die Versionen aufgeführt, die für die einzelnen Ressourcenanbieter und die API-Versionen der Profile unterstützt werden. Jeder Ressourcenanbieter enthält einen Satz mit Ressourcentypen und spezifischen Versionsnummern.

Für das API-Profil gelten drei Benennungskonventionen:

- **Neueste**
- **jjjj-mm-tt-hybrid**
- **jjjj-mm-tt-profile**

Eine Erklärung der API-Profile und des Rhythmus der Versionsveröffentlichung für Azure Stack finden Sie unter [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles.md).

> [!Note]  
> Das API-Profil **latest** enthält die neueste Version der Ressourcenanbieter-API und ist in diesem Artikel nicht aufgeführt.

## <a name="overview-of-2018---03-01-hybrid"></a>Übersicht über 2018 – 03-01-hybrid

| Ressourcenanbieter | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN Gateway lautet 2017-03-01 |
| Microsoft.Storage (Datenebene) | 2017-04-17 |
| Microsoft.Storage (Steuerungsebene) | 2016-01-01 |
| Microsoft. Web | 2016-08-01<br>Aktueller (derzeitiger) Stand in Azure |
| Microsoft.KeyVault | 2016-10-01 (Keine Änderung) |
| Microsoft.Resources         (Azure Resource Manager selbst) | 2016-02-01 |
| Microsoft.Authorization   (Richtlinienvorgänge) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Richtlinie | 2016-10-01 |
| Ressourcen | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Abonnements | 2016-10-01 |

Eine Liste mit den Versionen jedes einzelnen Ressourcentyps für die Anbieter im API-Profil finden Sie unter [Details zu 2018-03-01-hybrid](#details-for-the-2018-03-01-hybrid).

## <a name="details-for-the-2018-03-01-hybrid"></a>Details zu 2018-03-01-hybrid

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Mit der rollenbasierten Zugriffssteuerung werden die Aktionen verwaltet, die Benutzer in Ihrer Organisation für Ressourcen ausführen können. Diese Gruppe von Vorgängen ermöglicht das Definieren von Rollen, Zuweisen von Rollen zu Benutzern oder Gruppen und das Abrufen von Informationen zu Berechtigungen. Weitere Informationen finden Sie unter [Autorisierung](/rest/api/authorization/).

| Ressourcentypen | API-Versionen |
|---------------------|--------------------|
| Sperren | 2017-04-01 |
| Vorgänge | 2015-07-01 |
| Berechtigungen | 2015-07-01 |
| Richtlinienzuweisungen | 2016-12-01 (2017-06-01-preview) |
| Richtliniendefinitionen | 2016-12-01 |
| Anbietervorgänge | 2015-07-01-preview |
| Rollenzuweisungen | 2015-07-01 |
| Rollendefinitionen | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Ressourcentyp | API-Version |
|----------------------------------|----------------------|
| Delegierte Anbieterabonnements | 2015-06-01 – Vorschauversion |
| Delegierte Nutzungsaggregate | 2015-06-01 – Vorschauversion |
| Geschätzter Ressourcenaufwand | 2015-06-01 – Vorschauversion |
| Vorgänge | 2015-06-01 – Vorschauversion |
| Nutzungsaggregate von Abonnenten | 2015-06-01 – Vorschauversion |
| Nutzungsaggregate | 2015-06-01 – Vorschauversion |

### <a name="microsoftcompute"></a>Microsoft.Compute

Die Azure Compute-APIs ermöglichen Ihnen den programmgesteuerten Zugriff auf virtuelle Computer und die zugehörigen unterstützenden Ressourcen. Weitere Informationen finden Sie unter [Azure Compute](/rest/api/compute/).

| Ressourcentyp | API-Version |
|---------------------------------------------------------------|-------------|
| Verfügbarkeitsgruppen | 2016-03-30 |
| Standorte | 2016-03-30 |
| Speicherorte/Vorgänge | 2016-03-30 |
| Speicherorte/Herausgeber | 2016-03-30 |
| Speicherorte/Verwendungen | 2016-03-30 |
| Speicherorte/VM-Größen | 2016-03-30 |
| Vorgänge | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Virtuelle Computer/Erweiterungen | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| VM-Skalierungsgruppen/Erweiterungen | 2016-03-30 |
| VM-Skalierungsgruppen/Netzwerkschnittstellen | 2016-03-30 |
| VM-Skalierungsgruppen/virtuelle Computer | 2016-03-30 |
| VM-Skalierungsgruppen/virtuelle Computer/Netzwerkschnittstellen | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Ressourcentyp | API-Version |
|------------------|-------------|
| Zusammenstellung | 2015-04-01 |
| Inhalt der Zusammenstellung | 2015-04-01 |
| Auszug aus Zusammenstellung | 2015-04-01 |
| Katalogelemente | 2015-04-01 |
| Vorgänge | 2015-04-01 |
| Portal | 2015-04-01 |
| Suchen, | 2015-04-01 |
| Vorschlagen | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Ressourcentypen | API-Versionen |
|--------------------|--------------------|
| Vorgänge | 2015-04-01 |
| Ereignistypen | 2015-04-01 |
| Ereigniskategorien | 2015-04-01 |
| Metrikdefinitionen | 2018-01-01 |
| Metriken | 2018-01-01 |
| Diagnoseeinstellungen | 2017-05-01-preview |
| Kategorien von Diagnoseeinstellungen | 2017-05-01-preview |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Hiermit verwalten Sie Ihre Schlüsseltresore sowie die Schlüssel, Geheimnisse und Zertifikate in Ihren Schlüsseltresoren. Weitere Informationen finden Sie in der [Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault/).

| Ressourcentypen | API-Versionen |
|-------------------------|--------------|
| Vorgänge | 2016-10-01 |
| Tresore | 2016-10-01 |
| Tresore/Zugriffsrichtlinien | 2016-10-01 |
| Tresore/Geheimnisse | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Das Ergebnis des Vorgangsaufrufs ist eine Darstellung der Liste verfügbarer Netzwerkcloudvorgänge. Weitere Informationen finden Sie unter [REST-API für Vorgänge](/rest/api/operation/).

| Ressourcentypen | API-Versionen |
|---------------------------|--------------|
| Verbindungen | 2015-06-15 |
| DNS-Zonen | 2016-04-01 |
| Load Balancer | 2015-06-15 |
| Gateway des lokalen Netzwerks | 2015-06-15 |
| Standorte | 2016-04-01 |
| Speicherort/Vorgangsergebnisse | 2016-04-01 |
| Speicherorte/Vorgänge | 2016-04-01 |
| Speicherorte/Verwendungen | 2016-04-01 |
| Netzwerkschnittstellen | 2015-06-15 |
| Netzwerksicherheitsgruppen | 2015-06-15 |
| Vorgänge | 2015-06-15 |
| Öffentliche IP-Adresse | 2015-06-15 |
| Routingtabellen | 2015-06-15 |
| Gateway des virtuellen Netzwerks | 2015-06-15 |
| Virtuelle Netzwerke | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Mit Azure Resource Manager können Sie die Infrastruktur für Ihre Azure-Lösungen bereitstellen und verwalten. Sie organisieren zugehörige Ressourcen in Ressourcengruppen, und stellen Ihre Ressourcen mit JSON-Vorlagen bereit. Eine Einführung in die Bereitstellung und Verwaltung von Ressourcen mit Resource Manager finden Sie in der [Übersicht über Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

| Ressourcentypen | API-Versionen |
|-----------------------------------------|-------------------|
| Anwendungsregistrierungen | 2015-01-01 |
| Ressourcennamen prüfen | 2016-09-01 |
| Delegierte Anbieter | 2015-01-01 |
| Delegierte Anbieter/Angebote | 2015-01-01 |
| Delegierte Anbieter/Angebote/geschätzter Preis | 2015-01-01 |
| Bereitstellungen | 2016-09-01 |
| Bereitstellungen/Vorgänge | 2016-09-01 |
| Metadaten zu Erweiterungen | 2015-01-01 |
| Links | 2016-09-01 |
| Standorte | 2015-01-01 |
| Angebote | 2015-01-01 |
| Vorgänge | 2015-01-01 |
| Anbieter | 2017-08-01 |
| Ressourcengruppen | 2016-09-01 |
| Ressourcen | 2016-09-01 |
| Abonnements | 2016-09-01 |
| Abonnements/Speicherort | 2016-09-01 |
| Abonnements/Vorgangsergebnisse | 2016-09-01 |
| Abonnements/Anbieter | 2017-08-01 |
| Abonnements/Ressourcengruppen | 2016-09-01 |
| Abonnements/Ressourcengruppen/Ressourcen | 2016-09-01 |
| Abonnements/Ressourcen | 2016-09-01 |
| Abonnements/Tagnamen | 2016-09-01 |
| Abonnements/Tagnamen/Tagwerte | 2016-09-01 |
| Mandanten | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Mit dem Speicherressourcenanbieter (Storage Resource Provider, SRP) können Sie Ihr Speicherkonto und Ihre Schlüssel programmgesteuert verwalten. Weitere Informationen finden Sie in der [Referenz zur Ressourcenanbieter-REST-API für Azure Storage](/rest/api/storagerp/).

| Ressourcentypen | API-Versionen |
|-------------------------|--------------|
| Dient zum Prüfen der Verfügbarkeit des Namens. | 2016-01-01 |
| Standorte | 2016-01-01 |
| Speicherorte/Kontingente | 2016-01-01 |
| Vorgänge | 2016-01-01 |
| Speicherkonten | 2016-01-01 |
| Verwendungen | 2016-01-01 |

## <a name="details-for-the-2018-03-01-hybrid"></a>Details zu 2018-03-01-hybrid

### <a name="microsoft-authorization"></a>Microsoft-Autorisierung

| Ressourcentypen | API-Versionen |
|---------------------|---------------------------------|
| Sperren | 2017-04-01 |
| Vorgänge | 2015-07-01 |
| Berechtigungen | 2015-07-01 |
| Richtlinienzuweisungen | 2016-12-01 (2017-06-01-preview) |
| Richtliniendefinitionen | 2016-12-01 |
| Anbietervorgänge | 2015-07-01-preview |
| Rollenzuweisungen | 2015-07-01 |
| Rollendefinitionen | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Ressourcentyp | API-Version |
|---------------------------------------------------------------|-------------|
| Verfügbarkeitsgruppen | 2016-03-30 |
| Standorte | 2016-03-30 |
| Speicherorte/Vorgänge | 2016-03-30 |
| Speicherorte/Herausgeber | 2016-03-30 |
| Speicherorte/Verwendungen | 2016-03-30 |
| Speicherorte/VM-Größen | 2016-03-30 |
| Vorgänge | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Virtuelle Computer/Erweiterungen | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| VM-Skalierungsgruppen/Erweiterungen | 2016-03-30 |
| VM-Skalierungsgruppen/Netzwerkschnittstellen | 2016-03-30 |
| VM-Skalierungsgruppen/virtuelle Computer | 2016-03-30 |
| VM-Skalierungsgruppen/virtuelle Computer/Netzwerkschnittstellen | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Ressourcentypen | API-Versionen |
|---------------------------|--------------|
| Verbindungen | 2015-06-15 |
| DNS-Zonen | 2016-04-01 |
| Load Balancer | 2015-06-15 |
| Gateway des lokalen Netzwerks | 2015-06-15 |
| Standorte | 2016-04-01 |
| Speicherort/Vorgangsergebnisse | 2016-04-01 |
| Speicherorte/Vorgänge | 2016-04-01 |
| Speicherorte/Verwendungen | 2016-04-01 |
| Netzwerkschnittstellen | 2015-06-15 |
| Netzwerksicherheitsgruppen | 2015-06-15 |
| Vorgänge | 2015-06-15 |
| Öffentliche IP-Adresse | 2015-06-15 |
| Routingtabellen | 2015-06-15 |
| Gateway des virtuellen Netzwerks | 2015-06-15 |
| Virtuelle Netzwerke | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Ressourcentypen | API-Versionen |
|-----------------------------------------|--------------|
| Anwendungsregistrierungen | 2015-01-01 |
| Ressourcennamen prüfen | 2016-09-01 |
| Delegierte Anbieter | 2015-01-01 |
| Delegierte Anbieter/Angebote | 2015-01-01 |
| Delegierte Anbieter/Angebote/geschätzter Preis | 2015-01-01 |
| Bereitstellungen | 2016-09-01 |
| Bereitstellungen/Vorgänge | 2016-09-01 |
| Metadaten zu Erweiterungen | 2015-01-01 |
| Links | 2016-09-01 |
| Standorte | 2015-01-01 |
| Angebote | 2015-01-01 |
| Vorgänge | 2015-01-01 |
| Anbieter | 2017-08-01 |
| Ressourcengruppen | 2016-09-01 |
| Ressourcen | 2016-09-01 |
| Abonnements | 2016-09-01 |
| Abonnements/Speicherort | 2016-09-01 |
| Abonnements/Vorgangsergebnisse | 2016-09-01 |
| Abonnements/Anbieter | 2017-08-01 |
| Abonnements/Ressourcengruppen | 2016-09-01 |
| Abonnements/Ressourcengruppen/Ressourcen | 2016-09-01 |
| Abonnements/Ressourcen | 2016-09-01 |
| Abonnements/Tagnamen | 2016-09-01 |
| Abonnements/Tagnamen/Tagwerte | 2016-09-01 |
| Mandanten | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Ressourcentypen | API-Versionen |
|-------------------------|--------------|
| Dient zum Prüfen der Verfügbarkeit des Namens. | 2016-01-01 |
| Standorte | 2016-01-01 |
| Speicherorte/Kontingente | 2016-01-01 |
| Vorgänge | 2016-01-01 |
| Speicherkonten | 2016-01-01 |
| Verwendungen | 2016-01-01 |

## <a name="next-steps"></a>Nächste Schritte

- [Installieren von PowerShell für Azure Stack](../operator/azure-stack-powershell-install.md)
- [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](azure-stack-powershell-configure-user.md)  
