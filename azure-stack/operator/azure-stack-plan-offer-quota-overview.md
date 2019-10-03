---
title: Azure Stack – Übersicht über Pläne, Angebote, Kontingente und Abonnements | Microsoft-Dokumentation
description: Cloudbetreiber möchten verstehen, wie es sich mit Azure Stack-Plänen, -Angeboten, -Kontingenten und -Abonnements verhält.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 57d2fb7b9fb89858f13b3946be0e36d9529297a8
ms.sourcegitcommit: 1bae55e754d7be75e03af7a4db3ec43fd7ff3e9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319107"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Übersicht über Pläne, Angebote, Kontingente und Abonnements

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Mit [Azure Stack](azure-stack-overview.md) können Sie viele verschiedene Dienste bereitstellen, z. B. virtuelle Computer, SQL Server-Datenbanken, SharePoint, Exchange und sogar [Azure Marketplace-Elemente](azure-stack-marketplace-azure-items.md). Als Azure Stack-Bediener verwenden Sie Pläne, Angebote und Kontingente, um diese Dienste in Azure Stack zu konfigurieren und bereitzustellen.

Angebote enthalten mindestens einen Plan, und jeder Plan enthält mindestens einen Dienst. Indem Sie Pläne erstellen und in unterschiedlichen Angeboten kombinieren, können Sie Folgendes verwalten:

- Dienste und Ressourcen, auf die Ihre Benutzer zugreifen können
- Menge der Ressourcen, die von Benutzern genutzt werden können
- Regionen, die Zugriff auf Ressourcen haben

Die Bereitstellung eines Diensts umfasst folgende allgemeine Schritte:

1. Fügen Sie einen Dienst hinzu, den Sie für Ihre Benutzer bereitstellen möchten.
2. Erstellen Sie einen Plan, der mindestens einen Dienst enthält. Wählen Sie beim Erstellen eines Plans Kontingente aus, mit denen die Ressourceneinschränkungen der einzelnen Dienste des Plans definiert werden, oder erstellen Sie eigene Kontingente.
3. Erstellen Sie ein Angebot, das mindestens einen Plan enthält. Das Angebot kann Basispläne und optionale Add-On-Pläne enthalten.

Nachdem Sie das Angebot erstellt haben, kann es von Ihren Benutzern abonniert werden, um auf die entsprechenden Dienste und Ressourcen zuzugreifen. Benutzer können beliebig viele Angebote abonnieren. Die folgende Abbildung zeigt ein einfaches Beispiel für einen Benutzer, der zwei Angebote abonniert hat. Jedes Angebot verfügt über einen oder zwei Pläne, und jeder Plan ermöglicht den Zugriff auf diese Dienste.

![Mandantenabonnement mit Angeboten und Plänen](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Pläne

Pläne sind Gruppen von einem oder mehreren Diensten. Als Azure Stack-Bediener [erstellen Sie Pläne](azure-stack-create-plan.md), die Sie Ihren Benutzern anbieten können. Im Gegenzug abonnieren Ihre Benutzer die Angebote, um die darin enthaltenen Pläne und Dienste zu verwenden. Stellen Sie beim Erstellen von Plänen sicher, dass Sie Ihre Kontingente festlegen und Ihre Basispläne definieren. Außerdem können Sie erwägen, optionale Add-On-Pläne einzubinden.

### <a name="quotas"></a>Kontingente

Zur Vereinfachung der Cloudkapazitätsverwaltung können Sie vorkonfigurierte *Kontingente* verwenden oder für jeden Dienst in einem Plan ein neues Kontingent erstellen. Mit Kontingenten werden die Obergrenzen von Ressourcen definiert, die ein Benutzerabonnement bereitstellen oder nutzen kann. Beispielsweise kann ein Benutzer für ein Kontingent ggf. bis zu fünf virtuelle Computer erstellen.

> [!IMPORTANT]
> Es kann bis zu zwei Stunden dauern, bis neue Kontingente im Benutzerportal verfügbar sind oder ein geändertes Kontingent erzwungen wird.

Sie können Kontingente nach Region konfigurieren. Beispielsweise kann für einen Plan mit Computediensten in Region A ein Kontingent von zwei virtuellen Computern gelten.

>[!NOTE]
>Im Azure Stack Development Kit ist nur eine Region verfügbar (mit dem Namen *local*).

Weitere Informationen zu Kontingenttypen in Azure Stack finden Sie [hier](azure-stack-quota-types.md).

### <a name="base-plan"></a>Basisplan

Beim Erstellen eines Angebots kann der Dienstadministrator einen Basisplan einbinden. Diese Basispläne sind standardmäßig enthalten, wenn ein Benutzer dieses Angebot abonniert. Wenn Benutzer ein Angebot abonnieren, haben sie Zugriff auf alle Ressourcenanbieter, die in diesen Basisplänen angegeben sind (mit den entsprechenden Kontingenten).

### <a name="add-on-plans"></a>Add-On-Pläne

Add-On-Pläne sind optionale Pläne, die Sie einem Angebot hinzufügen können. Add-On-Pläne sind nicht standardmäßig im Abonnement enthalten. Add-On-Pläne sind zusätzliche Pläne (mit Kontingenten) in einem Angebot, die ein Abonnent seinen Abonnements hinzufügen kann. Beispielsweise können Sie einen Basisplan mit eingeschränkten Ressourcen für eine Testversion und einen Add-On-Plan mit umfassenderen Ressourcen für Kunden anbieten, die sich für die Nutzung des Diensts entscheiden.

## <a name="offers"></a>Angebote

Angebote sind Gruppen mit mindestens einem Plan, die Sie erstellen, damit Benutzer sie abonnieren können. Beispielsweise kann Angebot „Alpha“ den Plan A mit einem Satz von Computediensten und Plan B mit einem Satz von Speicher- und Netzwerkdiensten enthalten.

Beim [Erstellen eines Angebots](azure-stack-create-offer.md) müssen Sie mindestens einen Basisplan einschließen, aber Sie können auch Add-On-Pläne erstellen, die Benutzer ihrem Abonnement hinzufügen können.

## <a name="subscriptions"></a>Abonnements

Über ein Abonnement können Benutzer Ihre Angebote wahrnehmen. Wenn Sie ein Azure Stack-Bediener bei einem Dienstanbieter sind, erwerben Benutzer (Mandanten) Ihre Dienste, indem sie Ihre Angebote abonnieren. Wenn Sie ein Azure Stack-Bediener in einer Organisation sind, können Ihre Benutzer (Mitarbeiter) die von Ihnen angebotenen Dienste abonnieren, ohne dafür zu bezahlen.

Jede Kombination aus einem Benutzer und einem Angebot stellt ein eindeutiges Abonnement dar. Ein Benutzer kann über Abonnements für mehrere Angebote verfügen, aber jedes Abonnement gilt nur für ein Angebot. Pläne, Angebote und Kontingente gelten nur für ein eindeutiges Abonnement. Die gemeinsame Nutzung für mehrere Abonnements ist nicht möglich. Jede Ressource, die von einem Benutzer erstellt wird, ist einem Abonnement zugeordnet.

### <a name="default-provider-subscription"></a>Standardabonnement des Anbieters

Das Standardabonnement des Anbieters wird automatisch erstellt, wenn Sie das Azure Stack Development Kit bereitstellen. Dieses Abonnement kann zum Verwalten von Azure Stack, zum Bereitstellen weiterer Ressourcenanbieter sowie zum Erstellen von Plänen und Angeboten für Benutzer verwendet werden. Aus Sicherheits- und Lizenzierungsgründen sollte es nicht eingesetzt werden, um Kundenworkloads und -anwendungen auszuführen. Das Kontingent für das Standardabonnement des Anbieters kann nicht geändert werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Plänen und Angeboten finden Sie unter [Erstellen von Plänen in Azure Stack](azure-stack-create-plan.md).
