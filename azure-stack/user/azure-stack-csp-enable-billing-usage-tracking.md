---
title: Ermöglichen der Verwaltung Ihres Azure Stack-Abonnements durch Ihren Cloudlösungsanbieter | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Verwaltung Ihres Azure Stack-Abonnements durch Ihren Cloudlösungsanbieter (CSP) ermöglichen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: a11846feb852a44a5ae526e9c060ca1626bbe245
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961627"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-subscription"></a>Ermöglichen der Verwaltung Ihres Azure Stack-Abonnements durch Ihren Cloudlösungsanbieter

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Wenn Sie Azure Stack mit einem Cloudlösungsanbieter (Cloud Solution Provider, CSP) verwenden, können Sie Ihr eigenes Abonnement für den Zugriff auf Ressourcen in Azure und in Azure Stack verwalten. Sie können Ihr Abonnement auch vom Anbieter verwalten lassen. In diesem Artikel erfahren Sie Folgendes:

* Gewähren Sie Ihrem Dienstanbieter Zugriff auf Ihr Abonnement.
* Stellen Sie sicher, dass der Dienstanbieter, den Dienst verwalten kann.

> [!NOTE]
> Wenn der Cloudlösungsanbieter Ihr Konto nicht verwaltet und Sie die folgenden Schritte überspringen, kann der Cloudlösungsanbieter Ihr Azure Stack-Abonnement nicht für Sie verwalten.

## <a name="manage-your-subscription-with-a-csp"></a>Verwalten Ihres Abonnements per CSP

Fügen Sie den CSP Ihrem Abonnement als **Benutzer** hinzu.

1. Fügen Sie Ihren CSP als Gastbenutzer mit der Rolle **Benutzer** Ihrem Mandantenverzeichnis hinzu. Hilfe zum Hinzufügen eines Benutzers finden Sie unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Der CSP erstellt das lokale Azure Stack-Abonnement für Sie. Sie können mit der Verwendung von Azure Stack beginnen.

3. Ihr CSP sollte eine Ressource in Ihrem Abonnement erstellen, um sicherzustellen, dass er auch Ihre Ressourcen verwalten kann. Zum Beispiel müsste das [Erstellen eines virtuellen Windows-Computers mit dem Azure Stack-Portal](azure-stack-quick-windows-portal.md) möglich sein.

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Verwalten Ihres Abonnements über RBAC-Rechte durch den CSP

Fügen Sie den CSP Ihrem Abonnement als **Besitzer** hinzu.

1. Fügen Sie Ihren CSP Ihrem Mandantenverzeichnis als Gastbenutzer hinzu. Informationen zum Hinzufügen eines Benutzers finden Sie unter [Hinzufügen neuer Benutzer zu Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Fügen Sie dem CSP-Gastbenutzer die Rolle **Besitzer** hinzu. Informationen zum Hinzufügen eines CSP-Benutzers zu Ihrem Abonnement finden Sie unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](/azure/role-based-access-control/role-assignments-portal). Der CSP erstellt das lokale Azure Stack-Abonnement für Sie. Sie können mit der Verwendung von Azure Stack beginnen.
3. Ihr CSP sollte eine Ressource in Ihrem Abonnement erstellen, um sicherzustellen, dass er Ihre Ressourcen verwalten kann.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack finden Sie unter [Verbrauch und Abrechnung in Azure Stack](../operator/azure-stack-billing-and-chargeback.md).
