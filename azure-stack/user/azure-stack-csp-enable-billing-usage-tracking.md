---
title: Ermöglichen der Verwaltung Ihres Azure Stack-Abonnements durch Ihren Cloud-Dienstanbieter | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Verwaltung Ihres Azure Stack-Abonnements durch Ihren Cloud-Dienstanbieter (CSP) ermöglichen.
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
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 0dc162dd1d4021323f1bf80ad4f89fc721e575a9
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691973"
---
# <a name="let-your-cloud-service-provider-manage-your-azure-stack-subscription"></a>Ermöglichen der Verwaltung Ihres Azure Stack-Abonnements durch Ihren Cloud-Dienstanbieter

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

Wenn Sie Azure Stack mit einem Cloud-Dienstanbieter (Cloud Service Provider, CSP) verwenden, können Sie Ihr eigenes Abonnement für den Zugriff auf Ressourcen in Azure und in Azure Stack verwalten. Sie können Ihr Abonnement auch vom Anbieter verwalten lassen. In diesem Artikel erfahren Sie Folgendes:

* Gewähren Sie Ihrem Dienstanbieter Zugriff auf Ihr Abonnement.
* Stellen Sie sicher, dass der Dienstanbieter, den Dienst verwalten kann.

> [!NOTE]
> Wenn der CSP Ihr Konto nicht verwaltet und Sie die folgenden Schritte überspringen, ist die Verwaltung Ihres Azure Stack-Abonnements durch den CSP nicht möglich.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Verwalten Ihres Abonnements mit einem Cloud-Dienstanbieter

Fügen Sie den CSP Ihrem Abonnement als **Benutzer** hinzu.

1. Fügen Sie Ihren CSP als Gastbenutzer mit der Rolle **Benutzer** Ihrem Mandantenverzeichnis hinzu. Hilfe zum Hinzufügen eines Benutzers finden Sie unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Der CSP erstellt das lokale Azure Stack-Abonnement für Sie. Sie können mit der Verwendung von Azure Stack beginnen.
3. Ihr CSP sollte eine Ressource in Ihrem Abonnement erstellen, um sicherzustellen, dass er auch Ihre Ressourcen verwalten kann. Zum Beispiel müsste das [Erstellen eines virtuellen Windows-Computers mit dem Azure Stack-Portal](azure-stack-quick-windows-portal.md) möglich sein.

## <a name="let-the-cloud-service-provider-manage-your-subscription-using-rbac-rights"></a>Ermöglichen der Verwaltung Ihres Abonnements durch Ihren Cloud-Dienstanbieter mit RBAC-Rechten

Fügen Sie den CSP Ihrem Abonnement als **Besitzer** hinzu.

1. Fügen Sie Ihren CSP Ihrem Mandantenverzeichnis als Gastbenutzer hinzu. Hilfe zum Hinzufügen eines Benutzers finden Sie unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Fügen Sie dem CSP-Gastbenutzer die Rolle **Besitzer** hinzu. Hilfe zum Hinzufügen eines CSP-Benutzers zu Ihrem Abonnement finden Sie unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](/azure/role-based-access-control/role-assignments-portal). Der CSP erstellt das lokale Azure Stack-Abonnement für Sie. Sie können nun mit der Verwendung von Azure Stack beginnen.
3. Ihr CSP sollte eine Ressource in Ihrem Abonnement erstellen, um sicherzustellen, dass er Ihre Ressourcen verwalten kann.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack finden Sie unter [Verbrauch und Abrechnung in Azure Stack](../operator/azure-stack-billing-and-chargeback.md).
