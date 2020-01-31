---
title: 'Übersicht: Azure Stack Hub-Dienste, -Pläne, -Angebote und Abonnements'
description: Hier erhalten Sie eine Übersicht über Azure Stack Hub-Dienste, -Pläne, -Angebote und Abonnements.
author: BryanLa
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: c67d880308bd946c2f73f0993bbd3a19be0342aa
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885153"
---
# <a name="azure-stack-hub-services-plans-offers-subscriptions-overview"></a>Übersicht: Azure Stack Hub-Dienste, -Pläne, -Angebote und Abonnements

[Microsoft Azure Stack Hub](azure-stack-overview.md) ist eine Hybrid-Cloudplattform, die das Bereitstellen von Diensten aus Ihrem Datencenter ermöglicht. Zu diesen Diensten gehören u. a. VMs, SQL Server-Datenbanken, SharePoint, Exchange und sogar [Azure Marketplace-Elemente](azure-stack-marketplace-azure-items.md). Als Dienstanbieter können Sie Dienste für Ihre Mandanten anbieten. Innerhalb eines Unternehmens oder einer Behörde ist es möglich, den Mitarbeitern lokale Dienste anzubieten.

## <a name="overview"></a>Übersicht

Als Azure Stack Hub-Bediener verwenden Sie Angebote, Pläne und Abonnements, um Dienste zu konfigurieren und bereitzustellen. Angebote enthalten mindestens einen Plan, und jeder Plan enthält einen oder mehrere Dienste, die jeweils mit Kontingenten konfiguriert sind. Indem Sie Pläne erstellen und diese in verschiedenen Angeboten kombinieren, können Benutzer Ihre Angebote abonnieren und Ressourcen bereitstellen. Diese Struktur ermöglicht Ihnen die Verwaltung von Folgendem:

- Dienste und Ressourcen, auf die Ihre Benutzer zugreifen können
- Menge der Ressourcen, die von Benutzern genutzt werden können
- Regionen, die Zugriff auf Ressourcen haben

Die Bereitstellung eines Diensts umfasst folgende allgemeine Schritte:

1. Planen Ihrer Dienstangebote mit:

   - Grundlegenden Diensten, z. B. Compute, Speicher, Netzwerk oder Key Vault
   - Mehrwertdienste, z. B. App Service, SQL Server oder MySQL Server.

2. Erstellen Sie einen Plan, der mindestens einen Dienst umfasst. Wählen Sie beim Erstellen eines Plans Kontingente aus, mit denen die Ressourceneinschränkungen der einzelnen Dienste des Plans definiert werden, oder erstellen Sie eigene Kontingente.
3. Erstellen Sie ein Angebot, das mindestens einen Plan umfasst. Das Angebot kann Basispläne und optionale Add-On-Pläne enthalten.

Nachdem Sie das Angebot erstellt haben, können Ihre Benutzer es abonnieren, um auf die Dienste zuzugreifen und Ressourcen bereitzustellen. Benutzer können beliebig viele Angebote abonnieren. Die folgende Abbildung zeigt ein einfaches Beispiel für einen Benutzer, der zwei Angebote abonniert hat. Jedes Angebot verfügt über einen oder zwei Pläne, und jeder Plan ermöglicht den Zugriff auf bestimmte Dienste.

![Mandantenabonnement mit Angeboten und Plänen](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Dienste

Sie können [IaaS-Dienste](https://azure.microsoft.com/overview/what-is-iaas/) (Infrastructure-as-a-Service) anbieten, mit denen Benutzer bedarfsgesteuert eine Computinginfrastruktur erstellen können, die über das Azure Stack Hub-Benutzerportal bereitgestellt und verwaltet wird.

Sie können auch [PaaS-Dienste](https://azure.microsoft.com/overview/what-is-paas/) (Platform-as-a-Service) von Microsoft und anderen Drittanbietern für Azure Stack Hub bereitstellen. Die PaaS-Dienste, die Sie bereitstellen können, umfassen u.a.:

- [App Service](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL Server](azure-stack-mysql-resource-provider-deploy.md)

Sie können auch Dienste kombinieren, um komplexe Lösungen für verschiedene Benutzer zu integrieren und zu erstellen.

### <a name="quotas"></a>Kontingente

Zur Vereinfachung der Cloudkapazitätsverwaltung können Sie vorkonfigurierte *Kontingente* verwenden oder für jeden Dienst in einem Plan ein neues Kontingent erstellen. Mit Kontingenten werden die Obergrenzen von Ressourcen definiert, die ein Benutzerabonnement bereitstellen oder nutzen kann. Mithilfe eines Kontingents kann ein Benutzer beispielsweise bis zu fünf virtuelle Computer erstellen.

> [!IMPORTANT]
> Es kann bis zu zwei Stunden dauern, bis neue Kontingente im Benutzerportal verfügbar sind oder ein geändertes Kontingent erzwungen wird.

Sie können Kontingente nach Region einrichten. Beispielsweise kann für einen Plan mit Computediensten in Region A ein Kontingent von zwei virtuellen Computern gelten.

>[!NOTE]
>Im Azure Stack Development Kit (ASDK) ist nur eine Region verfügbar (mit dem Namen *local*).

Weitere Informationen zu Kontingenttypen in Azure Stack Hub finden Sie [hier](azure-stack-quota-types.md).

## <a name="plans"></a>Pläne

Pläne sind Gruppen von einem oder mehreren Diensten. Als Azure Stack Hub-Bediener [erstellen Sie Pläne](azure-stack-create-plan.md), die Sie Ihren Benutzern anbieten können. Im Gegenzug abonnieren Ihre Benutzer die Angebote, um die darin enthaltenen Pläne und Dienste zu verwenden. Stellen Sie beim Erstellen von Plänen sicher, dass Sie Ihre Kontingente festlegen und Ihre Basispläne definieren. Außerdem können Sie erwägen, optionale Add-On-Pläne einzubinden.

### <a name="base-plan"></a>Basisplan

Beim Erstellen eines Angebots kann der Dienstadministrator einen Basisplan einbinden. Diese Basispläne sind standardmäßig enthalten, wenn ein Benutzer dieses Angebot abonniert. Wenn Benutzer ein Angebot abonnieren, haben sie Zugriff auf alle Ressourcenanbieter, die in diesen Basisplänen angegeben sind (mit den entsprechenden Kontingenten).

### <a name="add-on-plans"></a>Add-On-Pläne

Add-On-Pläne sind optionale Pläne, die Sie einem Angebot hinzufügen können. Add-On-Pläne sind nicht standardmäßig im Abonnement enthalten. Add-On-Pläne sind zusätzliche Pläne (mit Kontingenten) in einem Angebot, die ein Abonnent seinen Abonnements hinzufügen kann. Beispielsweise können Sie einen Basisplan mit eingeschränkten Ressourcen für eine Testversion und einen Add-On-Plan mit umfassenderen Ressourcen für Kunden anbieten, die sich für die Nutzung des Diensts entscheiden.

## <a name="offers"></a>Angebote

Angebote sind Gruppen mit mindestens einem Plan, die Sie erstellen, damit Benutzer sie abonnieren können. Beispiel: Das Angebot „Alpha“ kann den Plan A mit mehreren Computediensten und Plan B mit mehreren Speicher- und Netzwerkdiensten enthalten.

Beim [Erstellen eines Angebots](azure-stack-create-offer.md) müssen Sie mindestens einen Basisplan einschließen, aber Sie können auch Add-On-Pläne erstellen, die Benutzer ihrem Abonnement hinzufügen können.

Berücksichtigen Sie beim Planen Ihrer Angebote Folgendes:

**Testangebote**: Testangebote sind praktisch, um neue Benutzer zu gewinnen, die dann ein Upgrade für zusätzliche Dienste durchführen können. Erstellen Sie für ein Testangebot einen kleinen [Basisplan](service-plan-offer-subscription-overview.md#base-plan) mit einem optionalen größeren Add-On-Plan. Alternativ können Sie ein Testangebot erstellen, das aus einem kleinen Basisplan und einem separaten Angebot mit einem größeren Plan mit nutzungsbasierter Bezahlung besteht.

**Kapazitätsplanung**: Möglicherweise haben Sie Bedenken, dass Benutzer große Mengen an Ressourcen abrufen und das System für alle Benutzer lahmlegen. Sie können [Ihre Pläne mit Kontingenten konfigurieren](service-plan-offer-subscription-overview.md#plans), um eine Obergrenze für die Nutzung festzulegen und so die Leistung zu verbessern.

**Delegierte Anbieter**: Bieten Sie anderen die Möglichkeit, Angebote in Ihrer Umgebung zu erstellen. Wenn Sie z.B. ein Dienstanbieter sind, können Sie diese Möglichkeit an Ihre Wiederverkäufer [delegieren](azure-stack-delegated-provider.md). Oder Unternehmen nutzen diese Möglichkeit, um solche Optionen an bestimmte Abteilungen oder Niederlassungen zu delegieren.

## <a name="subscriptions"></a>Abonnements

Über Abonnements können Benutzer auf Ihre Angebote zugreifen. Wenn Sie ein Azure Stack Hub-Bediener bei einem Dienstanbieter sind, erwerben Benutzer (Mandanten) Ihre Dienste, indem sie Ihre Angebote abonnieren. Wenn Sie ein Azure Stack Hub-Bediener in einer Organisation sind, können Ihre Benutzer (Mitarbeiter) die von Ihnen angebotenen Dienste abonnieren, ohne dafür zu bezahlen.

Benutzer erstellen neue Abonnements und erhalten Zugriff auf vorhandene Abonnements, indem sie sich bei Azure Stack Hub anmelden. Jedes Abonnement stellt eine Zuordnung zu einem einzelnen Angebot dar. Das Angebot (und seine Pläne und Kontingente), das einem Abonnement zugewiesen ist, kann nicht mit anderen Abonnements gemeinsam genutzt werden. Jede Ressource, die von einem Benutzer erstellt wird, ist einem Abonnement zugeordnet.

### <a name="default-provider-subscription"></a>Standardabonnement des Anbieters

Das Standardabonnement des Anbieters wird automatisch erstellt, wenn Sie das ASDK bereitstellen. Dieses Abonnement kann zum Verwalten von Azure Stack Hub, zum Bereitstellen weiterer Ressourcenanbieter sowie zum Erstellen von Plänen und Angeboten für Benutzer verwendet werden. Aus Sicherheits- und Lizenzierungsgründen darf es nicht eingesetzt werden, um Kundenworkloads und -Apps auszuführen. Das Kontingent für das Standardabonnement des Anbieters kann nicht geändert werden.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mehr über das Erstellen von Plänen, Angeboten und Abonnements erfahren möchten, beginnen Sie mit [Erstellen von Plänen](azure-stack-create-plan.md).
