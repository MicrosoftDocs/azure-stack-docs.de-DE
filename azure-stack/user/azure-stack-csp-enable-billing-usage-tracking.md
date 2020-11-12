---
title: Ermöglichen der Verwaltung Ihres Azure Stack Hub-Abonnements durch Ihren Cloudlösungsanbieter
description: Es wird beschrieben, wie Sie die Verwaltung Ihres Azure Stack Hub-Abonnements durch Ihren Cloudlösungsanbieter (Cloud Solution Provider, CSP) ermöglichen.
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: d54b73595b3f8f79cfa606a1fb41ad945f40157d
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414111"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-hub-subscription"></a>Ermöglichen der Verwaltung Ihres Azure Stack Hub-Abonnements durch Ihren Cloudlösungsanbieter

Wenn Sie Azure Stack Hub mit einem Cloudlösungsanbieter (Cloud Solution Provider, CSP) verwenden, können Sie Ihr eigenes Abonnement für den Zugriff auf Ressourcen in Azure und in Azure Stack Hub verwalten. Sie können Ihr Abonnement auch vom Anbieter verwalten lassen. In diesem Artikel lernen Sie Folgendes:

* Gewähren Sie Ihrem Dienstanbieter Zugriff auf Ihr Abonnement.
* Stellen Sie sicher, dass der Dienstanbieter, den Dienst verwalten kann.

> [!NOTE]
> Wenn der Cloudlösungsanbieter Ihr Konto nicht verwaltet und Sie die folgenden Schritte überspringen, kann der Cloudlösungsanbieter Ihr Azure Stack Hub-Abonnement nicht für Sie verwalten.

## <a name="manage-your-subscription-with-a-csp"></a>Verwalten Ihres Abonnements per CSP

Fügen Sie den CSP Ihrem Abonnement als **Benutzer** hinzu.

1. Fügen Sie Ihren CSP als Gastbenutzer mit der Rolle **Benutzer** Ihrem Mandantenverzeichnis hinzu. Hilfe zum Hinzufügen eines Benutzers finden Sie unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Der CSP erstellt das lokale Azure Stack Hub-Abonnement für Sie. Sie können mit der Verwendung von Azure Stack Hub beginnen.

3. Ihr CSP sollte eine Ressource in Ihrem Abonnement erstellen, um sicherzustellen, dass er auch Ihre Ressourcen verwalten kann. Zum Beispiel müsste das [Erstellen eines virtuellen Windows-Computers mit dem Azure Stack Hub-Portal](azure-stack-quick-windows-portal.md) möglich sein.

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Verwalten Ihres Abonnements über RBAC-Rechte durch den CSP

Fügen Sie den CSP Ihrem Abonnement als **Besitzer** hinzu.

1. Fügen Sie Ihren CSP Ihrem Mandantenverzeichnis als Gastbenutzer hinzu. Informationen zum Hinzufügen eines Benutzers finden Sie unter [Hinzufügen neuer Benutzer zu Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Fügen Sie dem CSP-Gastbenutzer die Rolle **Besitzer** hinzu. Informationen zum Hinzufügen eines CSP-Benutzers zu Ihrem Abonnement finden Sie unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](/azure/role-based-access-control/role-assignments-portal). Der CSP erstellt das lokale Azure Stack Hub-Abonnement für Sie. Sie können mit der Verwendung von Azure Stack Hub beginnen.
3. Ihr CSP sollte eine Ressource in Ihrem Abonnement erstellen, um sicherzustellen, dass er Ihre Ressourcen verwalten kann.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack Hub finden Sie unter [Verbrauch und Abrechnung in Azure Stack Hub](../operator/azure-stack-billing-and-chargeback.md).
