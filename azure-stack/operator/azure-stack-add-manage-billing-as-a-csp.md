---
title: Verwalten der Nutzung und Abrechnung für Azure Stack als Cloudlösungsanbieter | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Stack als Cloudlösungsanbieter (Cloud Solution Provider, CSP) registrieren und Kunden für die Abrechnung hinzufügen.
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
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e14c57cb5d0444c68619e6be5db688d7b1542e93
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961887"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-solution-provider"></a>Verwalten der Nutzung und Abrechnung für Azure Stack als Cloudlösungsanbieter

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird beschrieben, wie Sie Azure Stack als Cloudlösungsanbieter (Cloud Solution Provider, CSP) registrieren und Kunden hinzufügen.

Als CSP arbeiten Sie mit Azure Stack mit unterschiedlichen Kunden. Jeder Kunde verfügt über ein CSP-Abonnement in Azure. Sie müssen die Nutzung von Ihrem Azure Stack zu jedem Benutzerabonnement leiten.

Die folgende Abbildung zeigt die erforderlichen Schritte, um Ihr Konto für gemeinsame Dienste auszuwählen und das Azure-Konto beim Azure Stack-Konto zu registrieren. Nach der Registrierung können Sie Ihre Endkunden integrieren:

[![Prozess zur Aktivierung der Nutzung und Verwaltung als Cloudlösungsanbieter](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "Prozess zur Aktivierung der Nutzung und Verwaltung als Cloudlösungsanbieter") ](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Erstellen eines CSP- oder APSS-Abonnements

### <a name="csp-subscription-types"></a>CSP-Abonnementtypen

Wählen Sie den Typ des Kontos für gemeinsame Dienste aus, das Sie für Azure Stack verwenden. Folgende Abonnementtypen können für die Registrierung einer mehrinstanzenfähigen Azure Stack-Instanz verwendet werden:

- Cloud Solution Provider
- Partner Shared Services-Abonnement

#### <a name="azure-partner-shared-services"></a>Azure Partner Shared Services

Azure Partner Shared Services-Abonnements (APSS) sind die bevorzugte Option für die Registrierung, wenn ein direkter CSP oder ein CSP-Verteiler die Azure Stack-Instanz betreibt.

APSS-Abonnements sind einem Mandanten für gemeinsame Dienste zugeordnet. Wenn Sie Azure Stack registrieren, geben Sie Anmeldeinformationen für ein Konto an, das Besitzer des Abonnements ist. Das Konto, das Sie verwenden, um Azure Stack zu registrieren, kann sich von dem Administratorkonto unterscheiden, das Sie für die Bereitstellung verwenden. Außerdem müssen die beiden Konten nicht zur selben Domäne gehören. Sie können auch mithilfe eines bereits verwendeten Mandanten bereitstellen. Beispielsweise können Sie `ContosoCSP.onmicrosoft.com` verwenden und dann mit einem anderen Mandanten registrieren, z. B. `IURContosoCSP.onmicrosoft.com`. Sie müssen daran denken, sich mit `ContosoCSP.onmicrosoft.com` anzumelden, wenn Sie die tägliche Azure Stack-Verwaltung ausführen. Sie melden sich bei Azure mit `IURContosoCSP.onmicrosoft.com` an, wenn Sie Registrierungsvorgänge ausführen müssen.

Eine Beschreibung der APSS-Abonnements und wie Sie diese erstellen, finden Sie unter [Hinzufügen von Azure Partner Shared Services](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>CSP-Abonnements

CSP-Abonnements sind die bevorzugte Option für die Registrierung, wenn ein CSP-Vertriebspartner oder ein Endkunde die Azure Stack-Instanz betreibt.

## <a name="register-azure-stack"></a>Registrieren von Azure Stack

Verwenden Sie das APSS-Abonnement, das Sie mit den Informationen im vorherigen Abschnitt erstellt haben, um Azure Stack in Azure zu registrieren. Weitere Informationen finden Sie unter [Registrieren von Azure Stack in Ihrem Azure-Abonnement](azure-stack-registration.md).

## <a name="add-end-customer"></a>Hinzufügen eines Endkunden

Unter [Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack](azure-stack-csp-howto-register-tenants.md) erfahren Sie, wie Sie Azure Stack so konfigurieren, dass die Ressourcennutzung eines neuen Mandanten in dessen CSP-Abonnement gemeldet wird.

## <a name="charge-the-right-subscriptions"></a>Abrechnen mit den richtigen Abonnements

Azure Stack verwendet ein als *Registrierung* bezeichnetes Feature. Eine Registrierung ist ein in Azure gespeichertes Objekt. Das Registrierungsobjekt dokumentiert, welche Azure Abonnements für eine bestimmte Azure Stack-Instanz zur Abrechnung verwendet werden. Dieser Abschnitt behandelt die Wichtigkeit der Registrierung.

Mithilfe der Registrierung kann Azure Stack:

- Azure Stack-Nutzungsdaten an Azure Commerce weiterleiten und einem Azure-Abonnement in Rechnung stellen.
- Bei einer mehrinstanzenfähigen Azure Stack-Bereitstellung die Nutzung jedes Kunden in einem anderen Abonnement melden. Mehrinstanzenfähigkeit ermöglicht Azure Stack die Unterstützung verschiedener Organisationen in der gleichen Azure Stack-Instanz.

Für jede Azure Stack-Instanz sind ein Standardabonnement und zahlreiche Mandantenabonnements vorhanden. Das Standardabonnement ist ein Azure-Abonnement, das belastet wird, wenn kein mandantenspezifisches Abonnement vorhanden ist. Es muss das erste Abonnement sein, das registriert wird. Damit die mehrinstanzenfähige Nutzungsberichterstattung funktioniert, muss das Abonnement ein CSP- oder APSS-Abonnement sein.

Dann wird die Registrierung für jeden Mandanten mit einem Azure-Abonnement aktualisiert, der Azure Stack verwendet. Mandantenabonnements müssen vom CSP-Typ sein und zu dem Partner gehören, der das Standardabonnement besitzt. Sie können nicht die Kunden anderer registrieren.

Wenn Azure Stack Nutzungsinformationen an das globale Azure weiterleitet, konsultiert ein Dienst in Azure die Registrierung und ordnet die Nutzung jedes Mandanten dem entsprechenden Mandantenabonnement zu. Wenn ein Mandant nicht registriert wurde, wird dessen Nutzung an das Standardabonnement für die Azure Stack-Instanz weitergeleitet, von der sie stammt.

Da Mandantenabonnements CSP-Abonnements sind, wird ihre Rechnung an den CSP-Partner gesendet, und Nutzungsinformationen sind für den Endkunden nicht sichtbar.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über das CSP-Programm finden Sie unter [Programm für Cloud-Lösungsanbieter](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack finden Sie unter [Verbrauch und Abrechnung in Azure Stack](azure-stack-billing-and-chargeback.md).
