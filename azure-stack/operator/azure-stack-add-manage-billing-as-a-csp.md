---
title: Verwalten der Nutzung und Abrechnung für Azure Stack Hub als Cloud Solution Provider| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Stack Hub als Cloud Solution Provider (CSP) registrieren und Kunden für die Abrechnung hinzufügen.
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
ms.openlocfilehash: 7fcd34efefa9c82085274837829f989908f751ba
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881092"
---
# <a name="manage-usage-and-billing-for-azure-stack-hub-as-a-cloud-solution-provider"></a>Verwalten der Nutzung und Abrechnung für Azure Stack Hub als Cloud Solution Provider

In diesem Artikel wird beschrieben, wie Sie Azure Stack Hub als Cloud Solution Provider (CSP) registrieren und Kunden hinzufügen.

Als CSP arbeiten Sie mit Azure Stack Hub mit unterschiedlichen Kunden. Jeder Kunde verfügt über ein CSP-Abonnement in Azure. Sie müssen Nutzungsdaten aus Ihrer Azure Stack Hub-Instanz an die einzelnen Benutzerabonnements leiten.

Die folgende Abbildung zeigt die erforderlichen Schritte, um Ihr Konto für gemeinsame Dienste auszuwählen und das Azure-Konto beim Azure Stack Hub-Konto zu registrieren. Nach der Registrierung können Sie Ihre Endkunden integrieren:

[![Verfahren zum Aktivieren der Nutzung und Verwaltung als Cloud Solution Provider](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "Verfahren zum Aktivieren der Nutzung und Verwaltung als Cloud Solution Provider")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Erstellen eines CSP- oder APSS-Abonnements

### <a name="csp-subscription-types"></a>CSP-Abonnementtypen

Wählen Sie den Typ des Kontos für gemeinsame Dienste aus, das Sie für Azure Stack Hub verwenden. Folgende Abonnementtypen können für die Registrierung einer mehrinstanzenfähigen Azure Stack Hub-Instanz verwendet werden:

- Cloud Solution Provider
- Partner Shared Services-Abonnement

#### <a name="azure-partner-shared-services"></a>Azure Partner Shared Services

Azure Partner Shared Services-Abonnements (APSS) sind die bevorzugte Option für die Registrierung, wenn ein direkter CSP oder ein CSP-Verteiler die Azure Stack Hub-Instanz betreibt.

APSS-Abonnements sind einem Mandanten für gemeinsame Dienste zugeordnet. Wenn Sie Azure Stack Hub registrieren, geben Sie Anmeldeinformationen für ein Konto an, das Besitzer des Abonnements ist. Das Konto, das Sie zur Registrierung von Azure Stack Hub verwenden, kann sich von dem Administratorkonto unterscheiden, das Sie für die Bereitstellung verwenden. Außerdem müssen die beiden Konten nicht zur selben Domäne gehören. Sie können auch mithilfe eines bereits verwendeten Mandanten bereitstellen. Beispielsweise können Sie `ContosoCSP.onmicrosoft.com` verwenden und dann mit einem anderen Mandanten registrieren, z. B. `IURContosoCSP.onmicrosoft.com`. Sie müssen daran denken, sich mit `ContosoCSP.onmicrosoft.com` anzumelden, wenn Sie die tägliche Azure Stack Hub-Verwaltung ausführen. Sie melden sich bei Azure mit `IURContosoCSP.onmicrosoft.com` an, wenn Sie Registrierungsvorgänge ausführen müssen.

Eine Beschreibung der APSS-Abonnements und wie Sie diese erstellen, finden Sie unter [Hinzufügen von Azure Partner Shared Services](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>CSP-Abonnements

CSP-Abonnements sind die bevorzugte Option für die Registrierung, wenn ein CSP-Vertriebspartner oder ein Endkunde die Azure Stack Hub-Instanz betreibt.

## <a name="register-azure-stack-hub"></a>Registrieren von Azure Stack Hub

Verwenden Sie das APSS-Abonnement, das Sie mit den Informationen im vorherigen Abschnitt erstellt haben, um Azure Stack Hub in Azure zu registrieren. Weitere Informationen finden Sie unter [Registrieren von Azure Stack Hub in Ihrem Azure-Abonnement](azure-stack-registration.md).

## <a name="add-end-customer"></a>Hinzufügen eines Endkunden

Unter [Hinzufügen von Mandanten für Nutzung und Abrechnung zu Azure Stack Hub](azure-stack-csp-howto-register-tenants.md) erfahren Sie, wie Sie Azure Stack Hub so konfigurieren, dass die Ressourcennutzung eines neuen Mandanten in dessen CSP-Abonnement gemeldet wird.

## <a name="charge-the-right-subscriptions"></a>Abrechnen mit den richtigen Abonnements

Azure Stack Hub verwendet ein als *Registrierung* bezeichnetes Feature. Eine Registrierung ist ein in Azure gespeichertes Objekt. Das Registrierungsobjekt dokumentiert, welche Azure Abonnements für eine bestimmte Azure Stack Hub-Instanz zur Abrechnung verwendet werden. Dieser Abschnitt behandelt die Wichtigkeit der Registrierung.

Die Registrierung ermöglicht Azure Stack Hub Folgendes:

- Azure Stack Hub-Nutzungsdaten können an Azure Commerce weitergeleitet und Azure-Abonnements in Rechnung gestellt werden.
- Bei einer mehrinstanzenfähigen Azure Stack Hub-Bereitstellung kann die Nutzung jedes Kunden in einem anderen Abonnement gemeldet werden. Dank Mehrinstanzenfähigkeit kann Azure Stack Hub verschiedene Organisationen in derselben Azure Stack Hub-Instanz unterstützen.

Für jede Azure Stack Hub-Instanz sind ein Standardabonnement und zahlreiche Mandantenabonnements vorhanden. Das Standardabonnement ist ein Azure-Abonnement, das belastet wird, wenn kein mandantenspezifisches Abonnement vorhanden ist. Es muss das erste Abonnement sein, das registriert wird. Damit die mehrinstanzenfähige Nutzungsberichterstattung funktioniert, muss das Abonnement ein CSP- oder APSS-Abonnement sein.

Anschließend wird die Registrierung mit einem Azure-Abonnement für jeden Mandanten aktualisiert, der Azure Stack Hub verwendet. Mandantenabonnements müssen vom CSP-Typ sein und zu dem Partner gehören, der das Standardabonnement besitzt. Sie können nicht die Kunden anderer registrieren.

Wenn Azure Stack Hub Nutzungsinformationen an die globale Azure-Instanz weiterleitet, konsultiert ein Dienst in Azure die Registrierung und ordnet die Nutzung jedes Mandanten dem entsprechenden Mandantenabonnement zu. Wenn ein Mandant nicht registriert wurde, wird dessen Nutzung an das Standardabonnement für die Azure Stack Hub-Instanz weitergeleitet, von der sie stammt.

Da Mandantenabonnements CSP-Abonnements sind, wird ihre Rechnung an den CSP-Partner gesendet, und Nutzungsinformationen sind für den Endkunden nicht sichtbar.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über das CSP-Programm finden Sie unter [Programm für Cloud-Lösungsanbieter](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Weitere Informationen zum Abrufen von Ressourcennutzungsinformationen aus Azure Stack Hub finden Sie unter [Verbrauch und Abrechnung in Azure Stack Hub](azure-stack-billing-and-chargeback.md).
